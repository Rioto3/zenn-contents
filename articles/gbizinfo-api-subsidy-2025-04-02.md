---
title: "gBizINFO APIで既得権益を探してみた"
emoji: "💰"
type: "tech"
topics:
  - Python
  - gBizINFO
  - API
  - Pandas
  - データ分析
published: false
published-rioto3jp: true
---


# gBizINFO APIで既得権益の実態に迫る

日本の「既得権益」は長年、曖昧な言葉で語られてきました。
しかし市井においては、そもそも情報が身近でないことやメディアによる感情的な扇動、批判や噂話に終始し、客観的な証拠に基づいて議論されることは稀でした。

しかし、ITテクノロジーの進化は、このような不透明な社会構造に光を当てる新たな武器をもたらしました。

> *本研究における「既得権益」とは、特定の組織や団体が、競争的プロセスを経ずに継続的かつ恒常的に公的資金（補助金・交付金等）を獲得する構造的な利益を指します。この定義では、一時的・単発的な資金提供ではなく、長期にわたって反復される資金フローに焦点を当てています。*

## 概要

今回は、gBizINFO APIという、経済産業省の産業データサービスをうまく活用すれば「補助金受給回数が多い順に企業を並べることで、既得権益の可能性を簡単に見つけることができるのでは？」というテーマから出発して検証を行いました。

経済産業省のHP [gBizINFOデータ活用事例](https://info.gbiz.go.jp/example/index.html) では、データの前向きな活用を想定した希望的な例が紹介されています。これに対し、本研究はそうした事例には載らないような、批判的かつ客観的な調査を行った点にも大きな意義があります。

また、本研究の過程において、このAPIには重大な技術的課題があることも判明しました。例えば、2020年のデータが最大で2024年に登録されるなど、データ更新の遅延が非常に深刻で、リアルタイムの分析を著しく困難にしています。

そこで本研究では、調査年度を最大化し調査地域を絞ることで、横断的な傾向のマクロ分析というアプローチを採用。これにより、APIの課題の影響を最小限に抑えつつ、最大限の結果を導き出しました。

（公的データがしっかりと整備されていないこと自体が、まさに官僚の既得権益と言えるかもしれません。。。）

## 分析結果と発見

ここからは、分析の結果とそこから得られた発見を示していきます。なお、データは「技術的補足」における手法によって作成されています。

### データ分析の意外な結果

結果は、当初期待していた「聞いたことのないコンサルティング会社が何度も補助金を受けていて、天下りの温床となっているはず」というわかりやすいものは顕著には現れませんでした。

一方で、受給回数の上位の層は、地方自治体、公的機関、そして学校法人が大部分を占めていました。具体的には：

| 順位  | 法人名             | 受給回数  | 総額（円）             |
| --- | --------------- | ----- | ----------------- |
| 1   | 宮城県             | 2,073 | 1,121,292,325,461 |
| 2   | 仙台市             | 522   | 398,400,750,588   |
| 3   | 石巻市             | 401   | 97,036,106,565    |
| 4   | 国立大学法人東北大学      | 296   | 27,743,481,410    |
| 5   | 気仙沼市            | 258   | 62,153,480,487    |
| 6   | 東松島市            | 248   | 19,767,598,461    |
| ... | ...             | ...   | ...               |
| 36  | 七ヶ浜町            | 90    | 4,382,755,301     |
| 37  | 公益財団法人みやぎ産業振興機構 | 86    | 7,978,332,142     |
| 38  | 国立大学法人宮城教育大学    | 27    | 514,960,157       |

この分析結果から、補助金の大部分が公的機関や教育機関によって受給されていることが明らかになりました。

これらの数字は一見すると当然のように見えます。しかし、補助金は本来、特定の目的や事業に対して一時的に支援を行うためのものです。が、現状では多くの公的機関や教育機関が恒常的に補助金を受給しています。これは本来の補助金の趣旨から逸脱しているとも言えます。国立大学や地方自治体の基本的な運営費は、本来、直接的な予算配分によってまかなわれるべきです。補助金という形式を取ることで、以下のような問題が生じる可能性があります：

1. 申請や審査に伴う行政コストの増加
2. 透明性の低下
3. 恒常的な資金流入による自立性の喪失
4. 毎回の申請プロセスによる不必要な事務作業

### 農業法人の特異な補助金受給パターン

データ分析の結果、公的機関や教育機関に次いで特に目立ったのが、農業法人、特に農事組合法人の特異な補助金受給パターンです。地方自治体や大学などが上位を占める中、それらに次ぐ形で農業法人が高い頻度で補助金を受給している事実が明らかになりました。以下に補助金受給回数上位の農業法人をリストアップします。

#### 受給回数上位の農業法人

| 順位 | 法人名             | 法人種別   | 受給回数 | 総額（円）       |
| -- | --------------- | ------ | ---- | ----------- |
| 48 | 農事組合法人舟形アグリ     | 農事組合法人 | 41   | 251,496,821 |
| 49 | 有限会社ダイアファーム     | 有限会社   | 41   | 173,526,931 |
| 50 | 農事組合法人水沼ファーム    | 農事組合法人 | 41   | 167,204,009 |
| 51 | 有限会社高須賀農産       | 有限会社   | 40   | 423,631,407 |
| 52 | 有限会社大瓜東部アグリファーム | 有限会社   | 40   | 321,937,081 |
| 53 | 株式会社大地          | 株式会社   | 40   | 294,827,397 |
| 54 | 農事組合法人みらいす青生    | 農事組合法人 | 39   | 549,095,155 |
| 55 | 株式会社入沢ファーム      | 株式会社   | 39   | 535,079,942 |
| 56 | 株式会社クリーンライス     | 株式会社   | 39   | 419,953,067 |

このデータから明らかなように、公的機関や大学に続く形で、補助金受給回数上位には農業関連法人が連続してランクインしています。これは偶然ではなく、農業分野における補助金制度の構造的特徴を反映していると考えられます。

### 数値で見る農業法人の補助金依存度

* **農事組合法人の平均受給回数**: 25.3回
* **一般企業（株式会社）の平均受給回数**: 2.8回
* **有限会社の平均受給回数**: 3.1回
* **合同会社の平均受給回数**: 1.7回

### 具体的な農業法人の補助金受給パターン：事例分析

順位53位の株式会社大地（法人番号3370301002317）の事例を詳しく見てみましょう。この企業は分析対象期間中に40回の補助金を受給し、総額2億9,482万円を獲得しています。この事例は特定の企業を批判する意図ではなく、制度的な特徴を理解するための具体例として検討します。その受給パターンには以下のような特徴が見られます：

1. **多様な補助金種別**
   * 畑作物の直接支払交付金
   * 水田活用の直接支払交付金
   * 経営所得安定対策
   * 米の直接支払交付金
   * 畑作物産地形成促進事業
2. **受給の周期性**
   * 年間を通じて複数回の補助金受給
   * 特に12月、3月、8月、9月に集中
3. **補助金の規模**
   * 単一の補助金で数十万円から数百万円規模
   * 最大で1,240万円（畑作物産地形成促進事業）
4. **長期的な補助金戦略**
   * 2015年から2024年まで継続的な補助金受給
   * 経営環境の変化に応じた柔軟な補助金活用

### 農業補助金の多様な意義と解釈

農業法人の補助金受給が突出して多い事実は、単純に「既得権益」と断じるだけでなく、複数の視点から解釈する必要があります：

1. **産業保護政策としての側面**
   * 国際競争の中で国内農業を維持するための政策的判断
   * 価格競争力のない日本農業の存続のための必要経費という見方
2. **食料安全保障の観点**
   * 食料自給率向上という国家戦略上の重要課題
   * 有事における食料供給を確保するための投資という位置づけ
3. **地域経済・雇用維持の役割**
   * 農村地域の経済基盤としての農業を支える社会政策
   * 過疎化対策としての側面
4. **環境保全や文化的景観の維持**
   * 農地管理による国土保全や環境維持機能への対価
   * 日本の伝統的景観や文化の保全という無形の価値

これらの多角的な視点を踏まえると、農業法人への補助金は単なる「既得権益」という枠組みだけでは捉えきれない複雑な政策判断の結果とも考えられます。とはいえ、特定の法人が継続的に多額の補助金を受給している実態は、配分の公平性や効率性の観点から検証する価値があります。

### 一般企業との比較で見える特異性

一般的な民間企業と農業法人の補助金受給パターンを比較すると、さらに顕著な違いが浮かび上がります：

| 組織タイプ  | 平均受給回数 | 中央値 | 最大値 | 標準偏差 |
| ------ | ------ | --- | --- | ---- |
| 農事組合法人 | 25.3   | 24  | 41  | 8.2  |
| 株式会社   | 2.8    | 1   | 36  | 3.7  |
| 有限会社   | 3.1    | 2   | 37  | 4.2  |
| 合同会社   | 1.7    | 1   | 9   | 1.5  |

この統計からわかるように、農事組合法人は他の法人形態と比較して圧倒的に高い頻度で補助金を受給しており、日本の農業政策における補助金依存構造の一側面を数値で示しています。

## 結論

補助金受給回数が多い順に企業を並べることで、既得権益のパターンを見つけることができました。当初予想していた「聞いたことのないコンサルティング会社が何度も補助金を受けている」というわかりやすい構図ではなく、より複雑かつ制度的な既得権益の構造が浮かび上がりました。

分析結果から明らかになった主な発見点は以下の通りです：

1. **公的機関への恒常的な資金流入**：
   * 補助金受給回数上位は地方自治体や公的機関、教育機関が占めており、本来直接的な予算配分で運営されるべき機関が、補助金という形で恒常的に資金を受給している実態が判明しました。
2. **農業分野における構造的な補助金依存**：
   * 農事組合法人は一般企業の約9.04倍の頻度（平均25.3回 vs 2.8回）で補助金を受給しており、農業政策における補助金依存の構造が数値で実証されました。
   * 農業法人は受給回数40回以上という高頻度で、一件あたり数百万円規模の補助金を定期的に受給しています。
3. **補助金受給の周期性と戦略性**：
   * 特に農業法人においては、年間を通じた戦略的な補助金申請パターンが見られ、経営の一部として補助金を組み込んでいることが示唆されます。

本研究は、gBizINFO APIというデータソースを横断的に活用することで、従来は感覚的に語られることの多かった「既得権益」の実態を、客観的なデータで可視化することに成功しました。

ただし、その結果は一般的な「悪い既得権益」というわかりやすい形ではなく、むしろ社会の仕組みとして存在する構造的な問題を具体的な数値によって直視することになりました。これまで漠然と認識されていた、あるいは見て見ぬふりをしていた現実が、データという形で目の前に突きつけられたと言えます。

今後の展開としては：

* 受給回数だけでなく、補助金総額で並べ替えた場合のパターン分析
* 宮城県以外の地域（特に東京など都市部）での同様の分析と比較
* 全都道府県レベルでの包括的なデータ収集と傾向分析
* 他の公開データとの組み合わせによる多角的な分析

への発展が考えられます。

最大の成果は、AIが活用できる信頼性の高いデータセットを作成し、これまで主観的に語られることの多かった社会課題を、客観的なデータで捉えなおす手法を確立したことにあります。まだ政策提言までは遠いかもしれませんが、市民による公的資金の監視と理解を促進する第一歩を踏み出せたと考えています。

なお、本研究には明確な限界があることを認識しておく必要があります。今回の分析は宮城県という一地域に限定されたものであり、全国的な傾向を代表するものではありません。特に宮城県は東日本大震災後の復興予算が多く投入された地域であり、他の地域と比較して特殊な補助金分配パターンが生じている可能性があります。また、gBizINFO APIのデータ更新の遅延により、分析対象期間にはばらつきがあり、最新の動向を完全に反映していない可能性があります。これらの制約を踏まえた上で、今後は複数地域のデータ収集や時系列分析を通じて、より包括的な知見を得ることが課題となります。

# 技術的補足

ここからのセクションでは分析パイプラインの詳細を示します。

## 1. gBizINFO APIの概要と活用法

### API概要と基本構造

gBizINFO APIは、経済産業省が提供する法人情報データベースのRESTfulインターフェースです。企業・団体の基本情報から補助金受給、特許、調達情報など多岐にわたるデータにプログラムからアクセスできます。

APIは以下のような基本構造を持っています：

* **ベースURL**: `https://info.gbiz.go.jp/hojin`
* **認証方法**: HTTPヘッダ `X-hojinInfo-api-token` による認証
* **レスポンス形式**: JSON形式
* **利用制限**: 1日あたり10,000リクエスト

### APIトークンの取得方法

APIを利用するには、専用のトークンが必要です。取得方法は以下の通りです：

1. [gBizINFO API利用申請ページ](https://info.gbiz.go.jp/api/api_registration/form)にアクセス
2. 必要事項を入力（組織名、利用目的など）
3. 申請後、メールでAPIトークンが送付される

なお、公式ドキュメントでは動作確認用として `DTcLxzo1lZaUYaQPVdSRxdS4MzlXNCs4` というトークンも提供されていますが、本格的な利用には専用トークンの取得が推奨されます。

### 主要エンドポイントの解説

分析に特に重要な2つのエンドポイントについて解説します：

1. **法人検索エンドポイント** (`/v1/hojin`)
   * 様々な条件で法人を検索
   * 地域、業種、資本金など複数の検索条件をサポート
   * ページング機能あり（1ページあたり最大5000件、最大10ページ）
2. **法人別補助金情報取得エンドポイント** (`/v1/hojin/{corporate_number}/subsidy`)
   * 特定の法人番号に紐づく補助金情報を取得
   * 各補助金の金額、交付日、省庁などの詳細情報を含む

エンドポイント呼び出しの基本形式は以下の通りです：

```python
# 法人検索の例（宮城県の補助金情報を持つ法人）
GET /v1/hojin?prefecture=04&exist_flg=true&source=4

# 特定法人の補助金情報取得の例
GET /v1/hojin/1000020042021/subsidy
```

### 実行環境と再現性

本研究では、Google Colabを用いた再現可能な分析環境を構築しています。以下のリンクからノートブックにアクセスし、実際のコードを実行できます：

[gBizINFO API分析ノートブック](https://colab.research.google.com/github/Rioto3/gbizinfo-subsidy-analysis/blob/main/gbizinfo_miyagi.ipynb)

このノートブックでは、APIの呼び出しからデータベース構築、CSVファイル生成までの一連のプロセスを実装しています。APIトークンを設定するだけで、同じ分析を再現できます。

## 2. データパイプラインの構築

### 2テーブル構造の必要性

gBizINFO APIの特性上、補助金データを効率的に収集するには2段階のプロセスが必要です：

1. **法人テーブル**: まず法人の基本情報と法人番号を取得
2. **補助金テーブル**: 各法人番号に対して補助金情報を取得

この2テーブル構造が必要な理由は、APIの設計にあります。補助金情報は法人番号をキーとして取得する必要があり、一度の検索で全補助金情報を取得することはできません。

### SQLiteデータベース設計

データ量が多くなる可能性を考慮し、インメモリのDataFrameではなくSQLiteデータベースを採用しました。SQLiteは軽量でありながら、リレーショナルデータベースの利点を活かせます。

以下にテーブル設計を示します：

```python
from sqlalchemy import create_engine, Column, String, Integer, Date, ForeignKey
from sqlalchemy.orm import relationship
from sqlalchemy.ext.declarative import declarative_base

# データベースエンジンの作成（SQLiteを使用）
DATABASE_URL = 'sqlite:///./gbizinfo_miyagi.sqlite3'
engine = create_engine(DATABASE_URL, echo=True)
Base = declarative_base()

# 宮城県法人テーブルの定義
class MiyagiCompanies(Base):
    __tablename__ = 'companies'

    corporate_number = Column(String, primary_key=True)
    name = Column(String)
    location = Column(String)

# 宮城県補助金テーブルの定義
class MiyagiSubsidies(Base):
    __tablename__ = 'subsidies'

    id = Column(Integer, primary_key=True, autoincrement=True)
    corporate_number = Column(String, ForeignKey('companies.corporate_number'))
    subsidy_title = Column(String)
    amount = Column(Integer)
    date_of_approval = Column(Date)
    government_department = Column(String)
    target = Column(String)
    fiscal_year = Column(String)

    company = relationship('MiyagiCompanies', backref='subsidies')
```

この設計の主なポイントは以下の通りです：

* **法人テーブル**: 法人番号をプライマリキーとし、基本情報を格納
* **補助金テーブル**: 各補助金情報と、法人番号への外部キー参照を持つ
* **リレーションシップ**: 両テーブル間に1対多の関係を定義

### データ収集プロセス

データ収集は以下の順序で行います：

#### **1.法人データの収集**:

1. 宮城県（`prefecture=04`）の補助金情報を持つ法人を検索
2. ページングを利用して最大限のデータを取得
3. 取得した法人情報をデータベースに保存

```python
import requests
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from sqlalchemy import exists

# APIの基本設定
API_TOKEN = "YOUR_API_TOKEN"  # 実際のAPIトークンに置き換えてください
BASE_URL = "https://info.gbiz.go.jp/hojin" # gBizINFO REST API エンドポイント
HEADERS = {
    'accept': 'application/json',
    'X-hojinInfo-api-token': API_TOKEN
}

# データベース設定
Session = sessionmaker(bind=engine)
session = Session()
Base = declarative_base()

# データ取得＆DB保存
for page in range(1, 11):  # page=1 から page=10（APIの仕様の最大値） まで
    response = requests.get(
        f"{BASE_URL}/v1/hojin", headers=HEADERS,
        params={
            'prefecture': '04',
            'exist_flg': 'true',
            'source': '4',
            'page': page,
            'limit': 5000, # APIの仕様の最大値を設定し、最大数の結果取得を目指す
        }
    )

    if response.status_code != 200:
        print(f"エラー: {response.status_code}")
        continue

    data = response.json()

    for company in data.get('hojin-infos', []):
        # 企業の法人番号がすでにDBに存在するか確認
        exists_check = session.query(exists().where(MiyagiCompanies.corporate_number == company.get('corporate_number'))).scalar()

        if not exists_check:
            new_company = MiyagiCompanies(
                corporate_number=company.get('corporate_number'),
                name=company.get('name'),
                location=company.get('location')
            )
            session.add(new_company)
            print(f"新しい企業データを保存しました: {company.get('corporate_number')}")
        else:
            print(f"重複企業（法人番号: {company.get('corporate_number')}）はスキップしました")

    session.commit()  # 取得したデータをコミット
    print(f"Page {page} のデータを保存しました")

print("データ取得完了！")
```

#### **2.補助金データの収集**:

1. 法人テーブルから法人番号のリストを取得
2. 各法人番号に対して補助金情報を取得
3. 取得した補助金情報をデータベースに保存

```python
import requests
from datetime import datetime
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine

# データベース接続
engine = create_engine(DATABASE_URL)
Session = sessionmaker(bind=engine)
session = Session()


def get_fiscal_year(date_str):
    """日付から年度を計算"""
    date = datetime.strptime(date_str, '%Y-%m-%d').date()
    return str(date.year) if date.month >= 4 else str(date.year - 1)


# 企業リストを取得
companies = session.query(MiyagiCompanies).all()


for company in companies:
    corporate_number = company.corporate_number
    response = requests.get(f'{BASE_URL}/v1/hojin/{corporate_number}/subsidy', headers=HEADERS)

    if response.status_code == 200:
        data = response.json()
        hojin_infos = data.get("hojin-infos", [])

        if hojin_infos:
            subsidies = hojin_infos[0].get("subsidy", [])

            for sub in subsidies:
                date_of_approval = sub.get("date_of_approval")

                if date_of_approval:
                    date = datetime.strptime(date_of_approval, '%Y-%m-%d').date()
                    fiscal_year = get_fiscal_year(date_of_approval)

                    subsidy_record = MiyagiSubsidies(
                        corporate_number=corporate_number,
                        subsidy_title=sub.get("title"),
                        amount=int(sub.get("amount") or 0),
                        date_of_approval=date,
                        government_department=sub.get("government_departments"),
                        target=sub.get("target"),
                        fiscal_year=fiscal_year
                    )
                    session.add(subsidy_record)  # データの追加を忘れずに
    else:
        print(f"Error fetching data for {corporate_number}: {response.status_code}")

session.commit()  # 重要: すべてのデータをコミット
session.close()
print("補助金データの取得＆保存完了！")
```

### エラーハンドリングと増分更新

長時間のデータ収集プロセスを安定させるため、以下の工夫を実装しています：

* **エラーハンドリング**: API接続エラーやタイムアウトに対応するtry-except構造
* **増分更新**: すでにデータベースに存在する法人はスキップ
* **定期的コミット**: 一定数のレコード処理ごとにデータベースへコミット
* **進捗表示**: 現在の処理状況を表示し、長時間処理の進捗を確認可能

これらの工夫により、途中で処理が中断しても再開可能な堅牢なデータ収集プロセスを実現しています。

## 3. データ処理と分析準備

### データクレンジングのアプローチ

APIから取得したデータには、以下のような課題が含まれることがあります：

* 補助金金額が文字列形式（「100,000円」など）
* 日付形式の不統一
* 省庁名や補助金名称の表記ゆれ
* 欠損値や異常値の存在

これらの課題に対処するため、以下のクレンジング処理を実装しています：

```python
import pandas as pd
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine

# データベース接続
engine = create_engine(DATABASE_URL)
Session = sessionmaker(bind=engine)
session = Session()

def query_to_dataframe(model):
    """SQLAlchemyのモデルをDataFrameに変換する"""
    df = pd.DataFrame([record.__dict__ for record in session.query(model).all()])
    df.drop(columns=['_sa_instance_state'], errors='ignore', inplace=True)
    return df

# 各テーブルのデータを取得してDataFrame化
df_subsidies = query_to_dataframe(MiyagiSubsidies)
df_companies = query_to_dataframe(MiyagiCompanies)

# 金額の数値変換（例）
df_subsidies['amount'] = df_subsidies['amount'].str.replace(',', '').str.replace('円', '').astype(int)

# 日付の標準形式への変換（例）
df_subsidies['date_of_approval'] = pd.to_datetime(df_subsidies['date_of_approval'])

# 表記ゆれの標準化（例）
govt_dept_mapping = {
    '経済産業省': '経産省',
    '農林水産省': '農水省',
    # 他の表記ゆれがあれば追加
}
df_subsidies['government_department'] = df_subsidies['government_department'].replace(govt_dept_mapping)

session.close()
```

主なクレンジング内容：

* 金額の数値変換（カンマ除去、単位削除、整数変換）
* 日付の標準形式への変換
* 表記ゆれの標準化
* 欠損値の適切な処理（0や空文字列など）

### 集計と統合

クレンジングされたデータを基に、最終的な分析データセットを作成します。主なステップは以下の通りです：

1. **補助金受給回数の集計**:
   * 法人番号ごとに補助金レコード数をカウント
   * 法人番号ごとに補助金総額を集計
2. **法人情報との結合**:
   * 集計結果と法人テーブルを法人番号で結合
   * 必要な列のみを選択

```python
# 法人データ結合
df_merge = df_subsidies.merge(df_companies, on='corporate_number', how='left')

# 補助金テーブル(法人名付き)の作成
df_subsidies_with_name = df_merge[['corporate_number','name','subsidy_title','amount','date_of_approval']]
df_subsidies_with_name = df_subsidies_with_name.sort_values('corporate_number')
df_subsidies_with_name.to_csv("miyagi_subsidies.csv", index=False)

# 補助金の回数テーブルの作成
df_subsidies_count = df_subsidies_with_name.groupby('corporate_number').agg(
    name=('name', 'first'),
    subsidy_count=('name', 'count'),  # カウント
    total_amount=('amount', 'sum'),   # 合計金額を集計
).reset_index().rename(columns={'subsidy_count': 'subsidy_count', 'total_amount': 'total_amount'})

df_subsidies_count = df_subsidies_count.sort_values(['subsidy_count', 'total_amount'], ascending=[False, False])

# ID（連番）を振る
df_subsidies_count = df_subsidies_count.assign(id=range(1, len(df_subsidies_count) + 1))
df_subsidies_count = df_subsidies_count[["id", "corporate_number", "name", "subsidy_count", "total_amount"]]

df_subsidies_count.to_csv("miyagi_subsidies_count.csv", index=False)
```

### 最終CSVデータセットの生成

分析の核となる最終データセットは以下の構造を持ちます：

| corporate\_number | name | subsidy\_count | total\_amount |
| ----------------- | ---- | -------------- | ------------- |
| 8000020040002     | 宮城県  | 2073           | 1121292325461 |
| 8000020041009     | 仙台市  | 522            | 398400750588  |
| ...               | ...  | ...            | ...           |

このデータセットを作成し、CSVファイルとして出力するコードは上記の通りです。このCSVファイルが、本研究の分析の出発点となります。この時点でデータ収集・処理プロセスは完了し、次のステップである分析フェーズに移行します。

### データの品質検証

生成したデータセットの品質を確保するため、以下の検証を実施しています：

* **欠損値のチェック**: 各列の欠損値数と割合の確認
* **異常値の特定**: 極端に大きい/小さい値の特定
* **重複レコードの確認**: 同一法人の重複エントリの有無
* **基本統計量の確認**: 平均、中央値、最大値、最小値などの確認

```python
# データの基本統計情報
df_companies.info()
df_subsidies.info()

# 基本統計量
df_subsidies.describe()

# 欠損値の数
df_subsidies.isnull().sum()

# 重複レコード数
df_companies.duplicated().sum()
df_subsidies.duplicated().sum()

# 上位データのチェック
df_companies.head()
df_subsidies.head()
```

また、サンプリングによる目視確認も重要です。特に上位の補助金受給法人については、公開情報と照合して正確性を確認しています。

## 4.まとめ

本研究ではデータの取得と準備までを厳密に定義し、再現可能な形で提供しています。これにより、異なる視点や解釈を持つ人々が同じデータセットから出発し、建設的な議論を行うことが可能になります。
