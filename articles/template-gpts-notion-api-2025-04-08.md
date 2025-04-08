---
title: "GPTsでNotion APIを叩くようにしてみたらやばかった"
emoji: "🧨"
type: "tech"
topics:
  - "notion"
  - "notionapi"
  - "openai"
  - "chatgpt"
  - "gpts"
published: false
---


OpenAI の DevDay で発表された、GPTs は、特定のタスクに特化したカスタムモデルを作成できる ChatGPT Plus で利用できる新しい機能です。作った GPTs は、自分だけで使うのはもちろん、友達にシェアしたり。ウェブ上で公開することもできます。


https://youtu.be/U9mJuUkhUzk


この GPTs の機能である Actions を使うと、OpenAPI Schema を元に、外部 API を ChatGPT エージェントが実行するようになります。

この機能を使って Notion などの様々なサービスと GPTs を繋げてみたので、そのデモと GPTs のつくりかたを解説します。

## デモ

Notion は、開発者が Notion のデータを操作し、外部アプリケーションやサービスと連携するための API が公開されています。

そこで、Notion API の OpenAPI スキーマを書いて、検索、データベースクエリ、ブロックの読み取りエンドポイントを覚えさせ、実際に GPTs に叩かせてみました。

ユーザーのプロンプトを元にエンドポイントを使い分けてくれてるのが分かると思います。プロンプト次第では一回のクエリにとどまらず複数回のクエリで答えを探そうとし出します。

少し専門的なプロンプトですが、システムプロンプトでチューニングすればもう少し簡略化できると思います。

![](https://storage.googleapis.com/zenn-user-upload/11dc420a2748-20250408.jpg)

各サービスのデータを GPT を噛ませるだけでここまでの結果が返ってくるのでかなり可能性を感じました。Notion 以外にもデジタル庁の法令 API や Discord のメッセージ送信なども試しましたが無事動作しました 🎉



## 追記

OAuth を使ってノーコードで使用できる GPTs を新しく作りました。

ChatGPT のウェブクライアントからログインするだけで Notion ワークスペースを検索することができます。

https://twitter.com/yutakobayashi__/status/1724481251783139805


> 💡 ログイン後に「Couldn't log in with plugin.」というエラーが出た場合は、言語設定を `en-US` にすると直る事が多いです。





## Internal API を使う場合のやりかた

### 1. API キーの発行とコネクトの追加

[https://www.notion.so/my-integrations](https://www.notion.so/my-integrations) から、Notion の Internal API キーを発行します。

この API キーは控えておいてください。

作成したら、Notion のページやデータベースページの右上の「...」メニューから、先程作成したインテグレーションを "コネクト" に追加します。

### 2. リバースプロキシの設定

Notion API を叩く際には `Notion-Version` ヘッダーが必要ですが、ChatGPT の Web UI では Authentication ヘッダーしか追加できません。

そのため、Cloudflare Workers を使って以下のようなリバースプロキシを作成しました：

```
https://reverse-proxy.yutakobayashi.workers.dev/<url>
```

このプロキシは `api.notion.com` にアクセスされたときだけ `Notion-Version` ヘッダーを追加するだけのものですが、不安であれば以下の GitHub リポジトリにコードが公開されているので、自分でホストして使うことも可能です：

[https://github.com/yutakobayashidev/workers/blob/main/workers/reverse-proxy/src/index.ts](https://github.com/yutakobayashidev/workers/blob/main/workers/reverse-proxy/src/index.ts)

### 3. GPTs の設定

GPTs の設定画面の「Configure」から、「Actions」を選択します。

OpenAPI のスキーマとして以下のような形式を指定します（このスキーマも GPT に手伝ってもらいながら作成されました）：

```json
{
  "openapi": "3.1.0",
  "info": {
    "title": "Notion API",
    "version": "v1.0.0"
  },
  "servers": [
    {
      "url": "https://reverse-proxy.yutakobayashi.workers.dev/https://api.notion.com"
    }
  ],
  "paths": {
    "/v1/blocks/{block_id}/children": {
      "get": {
        "description": "Retrieve children of a block",
        "operationId": "getBlockChildren",
        "tags": ["Blocks"],
        "parameters": [
          {
            "name": "block_id",
            "in": "path",
            "required": true,
            "description": "ID of the block or page ID to get the child process",
            "schema": {"type": "string"}
          },
          {
            "name": "start_cursor",
            "in": "query",
            "schema": {"type": "string"}
          },
          {
            "name": "page_size",
            "in": "query",
            "schema": {"type": "integer"}
          }
        ],
        "responses": {
          "200": {
            "description": "Successful response",
            "content": {
              "application/json": {
                "schema": {
                  "type": "object",
                  "properties": {
                    "object": {"type": "string", "enum": ["list"]},
                    "results": {"type": "array", "items": {"type": "object"}},
                    "next_cursor": {"type": "string"},
                    "has_more": {"type": "boolean"}
                  }
                }
              }
            }
          },
          "400": {"description": "Bad request"},
          "401": {"description": "Unauthorized"},
          "404": {"description": "Not Found"},
          "429": {"description": "Too Many Requests"},
          "500": {"description": "Internal Server Error"}
        }
      }
    },
    "/v1/search": {
      "post": {
        "description": "Search across all pages and databases",
        "operationId": "search",
        "tags": ["Search"],
        "requestBody": {
          "required": true,
          "content": {
            "application/json": {
              "schema": {
                "type": "object",
                "properties": {
                  "query": {"type": "string", "description": "Text to search for"},
                  "sort": {
                    "type": "object",
                    "properties": {
                      "direction": {"type": "string"}
                    }
                  }
                }
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "Successful response",
            "content": {
              "application/json": {
                "schema": {"type": "object"}
              }
            }
          }
        }
      }
    }
  }
}
```













