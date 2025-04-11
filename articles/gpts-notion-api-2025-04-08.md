---
title: "仕方がないから、GPTsでNotion APIの書き込み機能を実装した"
emoji: "✍️"
type: "tech"
topics:
  - "notion"
  - "notionapi"
  - "openai"
  - "chatgpt"
  - "gpts"
published: true
---


## はじめに

Notion公式のMCPが公開され、私も即座に取り込みましたが、もうこれがないとダメな体になってきました。

https://www.youtube.com/watch?v=NMmDnYKD0fE

これでもうNotionは単なるUIだと思っていたんですが、外出先でメモをしようと思ったら動かない。。

そういえば、MCPってローカルのNodejs環境で動いているんでスマホからじゃダメだったんすね。しかし手打ちでNotion登録するのはもはや面倒すぎる。

そこで、GPTsでやることにしました。簡単にネットで事例を調べると、ほとんどが読み取り機能のみ。そこで今回は、書き込み機能を追加してみたので紹介します。

## 対象読者

* Notion APIを使った読み取りGPTsを作成している方
（もし持っていなければ、[こちらの記事（yutakobayashiさんの記事）](https://zenn.dev/yutakobayashi/articles/gpts-notion-api)を参考に作成してきてください！）


## 1. デモ

https://x.com/rioto3_/status/1910165892308050166

実際に動作している様子です。料理のレシピを見ている流れから、GPTに料理の材料をメモしてもらっています。店と日付なども情報として加えることで、Notionで買い物メモとして応用できたりします。

## 2. 書き込み機能の実装方法

### propertiesAndChildrenStringパラメータの活用

Notion APIでページを作成・更新する際の大きな課題の一つが、複雑なJSONデータ構造の扱いです。特にGPTsのAction機能では、このような複雑なデータのやり取りに制限があります。

通常、Notion APIでページを作成する際には以下のような構造のJSONデータを送信します：

```javascript
{
  "parent": { "database_id": "xxxx" },
  "properties": { 
    // DBのプロパティ構造
  },
  "children": [ 
    // ページのコンテンツ
  ]
}
```

しかし、GPTsのActionsでは、OpenAPIで柔軟なリクエストボディを扱うことが難しいです。
> #### additionalProperties: false must always be set in objects
> `additionalProperties` controls whether it is allowable for an object to contain > additional keys / values that were not defined in the JSON Schema.
> 
> Structured Outputs only supports generating specified keys / values, so we  require developers to set `additionalProperties`: false to opt into Structured Outputs.
> https://platform.openai.com/docs/guides/structured-outputs?api-mode=responses#supported-schemas


その課題を解決するために、今回は`propertiesAndChildrenString`というパラメータを導入しました。これは、通常のproperties（プロパティ）とchildren（ブロック内容）を一つの文字列としてパッケージ化するアプローチです。

```javascript
{
  "parent": { "database_id": "xxxx" },
  "propertiesAndChildrenString": JSON.stringify({
    "properties": {
      // プロパティ部分
    },
    "children": [
      // ブロック部分
    ]
  })
}
```

リバースプロキシ側では、この文字列を再度JSONオブジェクトに変換し、Notion API用の正しい形式に組み立て直します。

### リバースプロキシの設定

https://github.com/Rioto3/gpts-notion-reverse-proxy-gtd/blob/main/src/worker.js

worker.jsの核心部分は以下のコードです。特にpropertiesAndChildrenStringを処理する部分に注目してください。


```javascript
// POSTとPATCHリクエストのボディを処理
if (['POST', 'PATCH'].includes(request.method)) {
  try {
    const requestData = await request.clone().json();
    
    // ここが核心！propertiesAndChildrenStringの処理
    if ((notionPath.endsWith('/v1/pages') || notionPath.match(/\/v1\/pages\/[^\/]+$/)) && 
        requestData.propertiesAndChildrenString) {
      try {
        const parsedData = JSON.parse(requestData.propertiesAndChildrenString);
        
        // 新しいリクエストデータを作成
        const newRequestData = {
          ...(requestData.parent && { parent: requestData.parent }),
          ...(parsedData.properties && { properties: parsedData.properties }),
          ...(parsedData.children && { children: parsedData.children }),
          ...(requestData.archived !== undefined && { archived: requestData.archived })
        };
        
        requestBody = JSON.stringify(newRequestData);
      } catch (e) {
        return new Response(JSON.stringify({ 
          error: 'Invalid propertiesAndChildrenString', 
          details: e.message 
        }), {
          status: 400,
          headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' }
        });
      }
    } else {
      // リクエストボディをそのまま使用
      requestBody = JSON.stringify(requestData);
    }
  } catch (e) {
    // JSONパースエラーの場合は、リクエストボディをそのまま使用
    const rawBody = await request.text();
    requestBody = rawBody;
  }
}
```


このコードがリバースプロキシ内で文字列をJSONに戻し、適切なNotion API形式に変換します。

### OpenAPI定義の実装

https://github.com/Rioto3/gpts-notion-reverse-proxy-gtd/blob/main/openapi.yml

GPTsのActionには以下のようなPOSTリクエストを設定します：


```yaml
  /v1/pages:
    post:
      description: >
        Notion ページを作成する（特殊な実装）

        🚨 重要: 標準的なNotion API仕様とは大きく異なる実装方法です。
        
        主な注意点:
        - プロパティと子要素を単一の文字列 (propertiesAndChildrenString) で渡す
        - JSONデータは完全にエスケープされた文字列として送信する必要がある
        - 標準のNotion APIドキュメントに記載のない独自の拡張仕様を使用

      x-important-notes:
        - このAPIを使用する際は、必ずJSONを完全にエスケープし、文字列として送信すること
        - プロパティと子要素の構造は通常のNotion API形式とは異なる
        - JSONの文字列内部は正確にフォーマットされている必要がある
        - エラーを避けるため、JSONのエスケープと構造に細心の注意を払うこと

      x-openai-isConsequential: false
      operationId: createPage
      tags:
        - Pages
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - parent
                - propertiesAndChildrenString
              properties:
                parent:
                  type: object
                  required:
                    - type
                    - database_id
                  properties:
                    type:
                      type: string
                      enum:
                        - database_id
                      default: database_id
                    database_id:
                      type: string
                propertiesAndChildrenString:
                  type: string
                  description: >
                    🔑 特殊な実装: JSONデータを完全にエスケープした文字列として送信
                    
                    注意:
                    - 通常のNotion APIとは異なる形式
                    - JSONは完全にエスケープされている必要がある
                    - プロパティと子要素を単一の文字列として渡す

              example:
                parent:
                  type: database_id
                  database_id: database_id_here
                propertiesAndChildrenString: '{\"properties\":{\"Name\":{\"title\":[{\"text\":{\"content\":\"新しいInboxタスク\"}}]}},\"children\":[{\"object\":\"block\",\"type\":\"heading_2\",\"heading_2\":{\"rich_text\":[{\"text\":{\"content\":\"タスクの詳細\"}}]}},{\"object\":\"block\",\"type\":\"paragraph\",\"paragraph\":{\"rich_text\":[{\"type\":\"text\",\"text\":{\"content\":\"ここに本文を入力します。これは一般的な段落です。\",\"link\":null}}]}}]}'
```

注目してほしいのは：

`propertiesAndChildrenString` というパラメータを使用していること。
リクエストボディを複雑な構造を文字列として扱っています。なお、GPTにこの特殊仕様を理解させてやるために`description`でしっかりと説明してやり`example`を示します。


なお、`x-openai-isConsequential: false` の設定により、認証の煩わしさを軽減しています。ClaudeでCPMを使う場合には結構出てくるので、GPTの方が単純用途では使い勝手がいいです。

### GPTsのインストラクション設定

GPTsのインストラクションは任意にセットしてください：
Instructions の例:

https://github.com/Rioto3/gpts-notion-reverse-proxy-gtd/blob/main/Instruction.md






