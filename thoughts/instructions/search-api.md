# SearchAPI.io 利用ガイド（Claude Code 用）

このドキュメントを Claude Code に渡して、やりたいことを伝えてください。

---

## 事前準備: API キーの設定

**このファイルに API キーを書かないでください。** 以下のどちらかの方法で事前に設定しておきます。

### 方法 A: ターミナルで設定（一時的）

```bash
export SEARCHAPI_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxx
```

### 方法 B: `.env` ファイルに書く（プロジェクト用）

プロジェクトのルートに `.env` ファイルを作成します。

```
SEARCHAPI_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxx
```

**必ず `.gitignore` に `.env` を追加してください。**

```
# .gitignore に追加
.env
```

---

## SearchAPI.io の概要

SearchAPI.io は、Google や Bing などの検索結果を JSON で取得できる SERP（検索結果ページ）API サービスです。

- エンドポイント: `https://www.searchapi.io/api/v1/search`
- 認証: クエリパラメータ `api_key` または `Authorization: Bearer <API_KEY>` ヘッダー
- メソッド: GET
- レスポンス: JSON

---

## 基本の実装パターン（Python）

```python
import requests
import os

api_key = os.environ["SEARCHAPI_API_KEY"]

response = requests.get(
    "https://www.searchapi.io/api/v1/search",
    params={
        "engine": "google",
        "q": "検索キーワード",
        "api_key": api_key,
    }
)

data = response.json()

# 検索結果（organic_results）を表示
for result in data.get("organic_results", []):
    print(result["title"])
    print(result["link"])
    print(result.get("snippet", ""))
    print()
```

---

## 利用可能な主要エンジン

| `engine` パラメータ | 用途 |
|---------------------|------|
| `google` | Google ウェブ検索 |
| `google_light` | Google 軽量版（高速・低コスト） |
| `google_news` | Google ニュース検索 |
| `google_images` | Google 画像検索 |
| `google_maps` | Google マップ検索 |
| `google_shopping` | Google ショッピング検索 |
| `google_scholar` | Google Scholar（学術論文）|
| `google_trends` | Google トレンド |
| `google_jobs` | Google 求人検索 |
| `google_ai_mode` | Google AI Mode（AI 要約付き）|
| `bing` | Bing ウェブ検索 |
| `duckduckgo` | DuckDuckGo ウェブ検索 |
| `youtube` | YouTube 動画検索 |

---

## よく使うオプションパラメータ

| パラメータ | 説明 | 例 |
|-----------|------|-----|
| `q` | 検索キーワード（必須） | `"AI Agent"` |
| `engine` | 検索エンジン（必須） | `"google"` |
| `gl` | 国コード | `"jp"` |
| `hl` | 言語コード | `"ja"` |
| `location` | 地域指定 | `"Tokyo,Japan"` |
| `num` | 取得件数 | `10` |
| `page` | ページ番号 | `2` |

---

## レスポンスの主な構造（Google の場合）

```json
{
  "search_metadata": { "status": "Success", ... },
  "search_parameters": { "q": "検索キーワード", ... },
  "organic_results": [
    {
      "position": 1,
      "title": "ページタイトル",
      "link": "https://example.com",
      "snippet": "ページの説明文..."
    }
  ],
  "knowledge_graph": { ... },
  "related_questions": [ ... ]
}
```

---

## 残りクレジットの確認

```python
response = requests.get(
    "https://www.searchapi.io/api/v1/me",
    params={"api_key": api_key}
)
print(response.json())
# → current_month_usage, monthly_allowance, remaining_credits が確認できる
```

---

## Claude Code への指示の出し方（例）

### 例 1: キーワード検索して結果をファイルに保存

> このドキュメントを参考に、「AI Agent フレームワーク 2026」を Google で検索して、結果のタイトル・URL・スニペットを Markdown ファイルに保存するスクリプトを作って。

### 例 2: 複数キーワードの一括検索

> このドキュメントを参考に、キーワードリスト（CSV）を読み込んで、各キーワードを SearchAPI.io で Google 検索し、結果を1つの CSV にまとめるスクリプトを作って。

### 例 3: ニュース収集

> このドキュメントを参考に、engine を google_news にして「生成AI 規制」の最新ニュースを取得し、日付・タイトル・URL の一覧を出力するスクリプトを作って。

### 例 4: 競合調査

> このドキュメントを参考に、競合3社（A社, B社, C社）の社名で Google 検索して、それぞれの上位10件の結果を比較表にまとめるスクリプトを作って。

---

## 注意事項

- API キーはコードに直接書かず、環境変数 `SEARCHAPI_API_KEY` から読み取ること
- 無料枠は月100リクエスト。超過分は契約プランによる
- レート制限があるため、大量検索時は間隔を空けること
- 公式ドキュメント: https://www.searchapi.io/docs/google