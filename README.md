<p align="center">
  <h1 align="center">FreeNews API</h1>
  <p align="center">
    Global news aggregation API with AI-powered analysis.<br/>
    50+ sources &bull; 20 languages &bull; Sentiment &amp; entity extraction &bull; Full-text search
  </p>
  <p align="center">
    <a href="https://freenews.site"><strong>Website</strong></a> &nbsp;&middot;&nbsp;
    <a href="https://freenews.site/register"><strong>Get Free API Key</strong></a> &nbsp;&middot;&nbsp;
    <a href="https://freenews.site/dashboard"><strong>Dashboard &amp; Playground</strong></a>
  </p>
</p>

---

## What is FreeNews?

FreeNews aggregates news from **50+ global sources** (BBC, CNN, Reuters, AP, and more), processes them with **AI analysis** (summarization, entity extraction, sentiment scoring, bilingual translation), and serves everything through a simple REST API.

**Use cases:**

- Build a news reader or dashboard
- Monitor media sentiment on topics or brands
- Feed structured news data into LLM / RAG pipelines
- Academic research on global media coverage
- Content curation and recommendation engines

**Highlights:**

| Feature | Description |
|---------|-------------|
| **50+ News Sources** | BBC, CNN, Reuters, AP, NHK, Le Monde, and more across 6 continents |
| **AI Analysis** | Bilingual summaries (EN/ZH), keywords, entity extraction, sentiment scoring, importance rating |
| **Full-Text Search** | Elasticsearch-powered search with advanced boolean query syntax |
| **20 Languages** | English, Chinese, Japanese, Korean, French, German, Spanish, Arabic, and more |
| **Free Tier** | Generous free plan to get started immediately |

---

## Quick Start

Get your first API response in 30 seconds:

### 1. Get your API key

Register at [freenews.site/register](https://freenews.site/register) and create an API key in the [Dashboard](https://freenews.site/dashboard).

### 2. Make your first request

```bash
curl "https://freenews.site/api/v1/news?pageSize=5" \
  -H "X-API-Key: YOUR_API_KEY"
```

### 3. Explore the data

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "id": 12345,
        "title": "Global Tech Summit Opens in Geneva",
        "summary": "World leaders and tech executives gather to discuss AI regulation...",
        "author": "Jane Smith",
        "language": "en",
        "url": "https://example.com/article/12345",
        "publishedAt": "2026-05-05T08:30:00",
        "source": {
          "id": 1,
          "name": "BBC News",
          "code": "bbc"
        },
        "aiAnalysis": {
          "titleZh": "全球科技峰会在日内瓦开幕",
          "summaryZh": "世界各国领导人和科技高管齐聚一堂，讨论人工智能监管...",
          "sentimentLabel": "neutral",
          "sentimentScore": 0.52,
          "importanceScore": 8,
          "categories": ["technology", "politics"],
          "keywordsEn": ["AI regulation", "Geneva", "tech summit"],
          "keywordsZh": ["AI监管", "日内瓦", "科技峰会"],
          "entities": [
            { "nameEn": "Geneva", "nameZh": "日内瓦", "type": "location" }
          ]
        }
      }
    ],
    "total": 128456,
    "page": 1,
    "pageSize": 5,
    "totalPages": 25692
  }
}
```

---

## Authentication

All data endpoints under `/api/v1/news`, `/api/v1/search`, and `/api/v1/account` require an API key.

Pass your key in **one** of two ways:

| Method | Example |
|--------|---------|
| **Header** (recommended) | `X-API-Key: fn_a1b2c3d4...` |
| **Query parameter** | `?api_key=fn_a1b2c3d4...` |

Metadata endpoints (`/api/v1/meta/*`) are **public** and do not require an API key.

---

## Response Format

All responses follow a unified JSON structure:

```json
{
  "code": 0,
  "message": "success",
  "data": { ... },
  "success": true
}
```

- `code` = `0` means success, `success` = `true`.
- Non-zero `code` indicates an error (`success` = `false`). See [Error Codes](#error-codes).
- HTTP status is typically `200` even for business errors; always check the `code` field.

---

## API Reference

**Base URL:** `https://freenews.site`

### Metadata (No API Key Required)

#### `GET /api/v1/meta/sources`

Returns all available news sources.

<details>
<summary>Response example</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "code": "bbc",
      "name": "BBC News",
      "url": "https://www.bbc.com",
      "description": "British Broadcasting Corporation",
      "country": "United Kingdom",
      "countryCode": "GB",
      "language": "en",
      "region": "Europe",
      "logoUrl": "https://...",
      "feedUrl": "https://feeds.bbci.co.uk/news/rss.xml"
    }
  ]
}
```
</details>

---

#### `GET /api/v1/meta/languages`

Returns all available languages with article counts.

<details>
<summary>Response example</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": [
    { "code": "en", "name": "English", "count": 85432 },
    { "code": "zh", "name": "中文", "count": 21056 },
    { "code": "ja", "name": "日本語", "count": 8901 }
  ]
}
```
</details>

---

#### `GET /api/v1/meta/stats`

Returns platform-wide statistics.

<details>
<summary>Response example</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "totalArticles": 128456,
    "aiAnalyzedArticles": 96213,
    "totalSources": 52,
    "totalLanguages": 18
  }
}
```
</details>

---

#### `GET /api/v1/meta/preview`

Returns the latest 6 articles (title + summary only). Useful for landing pages.

---

### News

#### `GET /api/v1/news`

Retrieve a paginated list of news articles with optional filters.

**Parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `page` | integer | No | `1` | Page number (starts from 1) |
| `pageSize` | integer | No | `20` | Items per page (max 100) |
| `source` | string | No | - | Filter by source code (e.g. `bbc`, `cnn`). See `/meta/sources` |
| `language` | string | No | - | Filter by language code (e.g. `en`, `zh`). See `/meta/languages` |
| `startTime` | string | No | - | Start time (UTC, ISO 8601). e.g. `2026-01-01T00:00:00` |
| `endTime` | string | No | - | End time (UTC, ISO 8601). e.g. `2026-01-31T23:59:59` |

**Example:**

```bash
curl "https://freenews.site/api/v1/news?source=bbc&language=en&pageSize=10" \
  -H "X-API-Key: YOUR_API_KEY"
```

<details>
<summary>Response</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "id": 12345,
        "title": "Article Title",
        "summary": "Brief summary of the article...",
        "author": "Author Name",
        "language": "en",
        "images": ["https://..."],
        "url": "https://original-article-url.com",
        "publishedAt": "2026-05-05T08:30:00",
        "collectedAt": "2026-05-05T09:00:00",
        "source": {
          "id": 1,
          "name": "BBC News",
          "code": "bbc"
        }
      }
    ],
    "total": 85432,
    "page": 1,
    "pageSize": 10,
    "totalPages": 8544
  }
}
```
</details>

---

#### `GET /api/v1/news/{id}`

Get full article details including AI analysis.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `id` | integer | Yes | Article ID (path parameter) |

**Example:**

```bash
curl "https://freenews.site/api/v1/news/12345" \
  -H "X-API-Key: YOUR_API_KEY"
```

<details>
<summary>Response</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 12345,
    "title": "Global Tech Summit Opens in Geneva",
    "summary": "World leaders and tech executives gather...",
    "content": "Full article content (availability depends on your plan)...",
    "contentTruncated": false,
    "author": "Jane Smith",
    "language": "en",
    "images": ["https://..."],
    "url": "https://example.com/article/12345",
    "publishedAt": "2026-05-05T08:30:00",
    "collectedAt": "2026-05-05T09:00:00",
    "source": {
      "id": 1,
      "name": "BBC News",
      "code": "bbc"
    },
    "aiAnalysis": {
      "titleZh": "全球科技峰会在日内瓦开幕",
      "titleEn": "Global Tech Summit Opens in Geneva",
      "summaryZh": "世界各国领导人和科技高管齐聚讨论AI监管...",
      "summaryEn": "World leaders and tech executives gather to discuss AI regulation...",
      "keywordsZh": ["AI监管", "日内瓦", "科技峰会"],
      "keywordsEn": ["AI regulation", "Geneva", "tech summit"],
      "entities": [
        { "nameZh": "日内瓦", "nameEn": "Geneva", "type": "location" },
        { "nameZh": "联合国", "nameEn": "United Nations", "type": "organization" }
      ],
      "categories": ["technology", "politics"],
      "sentimentScore": 0.52,
      "sentimentLabel": "neutral",
      "importanceScore": 8,
      "readingTime": 4
    }
  }
}
```
</details>

> **Note:** Full article content is available on paid plans. Free tier returns truncated content (first 500 characters).

---

### Search

#### `GET /api/v1/search`

Full-text search powered by Elasticsearch. Supports advanced query syntax.

**Parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `keyword` | string | No | - | Search query. Supports [advanced syntax](#advanced-search-syntax) |
| `source` | string | No | - | Filter by source code |
| `language` | string | No | - | Filter by language code |
| `sentiment` | string | No | - | Filter by sentiment: `positive`, `neutral`, `negative`, `mixed` |
| `categories` | string | No | - | Filter by categories (comma-separated). e.g. `technology,politics` |
| `searchIn` | string | No | all fields | Limit search scope: `title`, `content`, `summary`, `keywords` (comma-separated) |
| `startDate` | string | No | - | Start date (UTC, ISO format). e.g. `2026-01-01` |
| `endDate` | string | No | - | End date (UTC, ISO format). e.g. `2026-12-31` |
| `page` | integer | No | `1` | Page number |
| `pageSize` | integer | No | `20` | Items per page (max 100) |

**Example:**

```bash
curl "https://freenews.site/api/v1/search?keyword=artificial+intelligence&language=en&pageSize=5" \
  -H "X-API-Key: YOUR_API_KEY"
```

<details>
<summary>Response</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "total": 4523,
    "page": 1,
    "pageSize": 5,
    "pages": 905,
    "items": [
      {
        "id": 12345,
        "url": "https://example.com/article/12345",
        "sourceCode": "bbc",
        "sourceName": "BBC News",
        "language": "en",
        "title": "New AI Regulation Framework Proposed",
        "titleZh": "新的AI监管框架被提出",
        "summary": "European lawmakers propose comprehensive AI regulation...",
        "summaryZh": "欧洲立法者提出全面的AI监管方案...",
        "keywordsEn": ["AI", "regulation", "Europe"],
        "keywordsZh": ["人工智能", "监管", "欧洲"],
        "categories": ["technology", "politics"],
        "sentimentLabel": "neutral",
        "sentimentScore": 0.55,
        "importanceScore": 9,
        "entities": [
          { "nameEn": "European Union", "nameZh": "欧盟", "type": "organization" }
        ],
        "publishedAt": "2026-05-04T14:00:00",
        "createdAt": "2026-05-04T14:30:00",
        "score": 12.34,
        "highlight": {
          "title": ["New <em>AI</em> Regulation Framework Proposed"],
          "summary": ["European lawmakers propose comprehensive <em>AI</em> regulation..."]
        }
      }
    ]
  }
}
```
</details>

---

#### `POST /api/v1/search`

Same as `GET /api/v1/search`, but accepts a JSON body. Better for complex queries with many parameters.

**Request body:**

```json
{
  "keyword": "\"trade war\" AND (tariffs OR sanctions)",
  "language": "en",
  "sentiment": "negative",
  "categories": "politics,economy",
  "startDate": "2026-01-01",
  "endDate": "2026-05-01",
  "page": 1,
  "pageSize": 20
}
```

---

#### `GET /api/v1/search/suggest`

Returns search suggestions for autocomplete. **Does not consume quota.**

**Parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `q` | string | Yes | - | Search prefix (max 100 characters) |
| `limit` | integer | No | `10` | Max suggestions (1-50) |

**Example:**

```bash
curl "https://freenews.site/api/v1/search/suggest?q=artif&limit=5" \
  -H "X-API-Key: YOUR_API_KEY"
```

<details>
<summary>Response</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": [
    "artificial intelligence",
    "artificial general intelligence",
    "artificial neural networks"
  ]
}
```
</details>

---

### Account

#### `GET /api/v1/account/quota`

Check your current API key's quota and usage. **Does not consume quota.**

**Example:**

```bash
curl "https://freenews.site/api/v1/account/quota" \
  -H "X-API-Key: YOUR_API_KEY"
```

<details>
<summary>Response</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "keyName": "My App Key",
    "keyPrefix": "fn_a1b2...x9z0",
    "status": "active",
    "planName": "free",
    "planDisplayName": "Free",
    "callsToday": 42,
    "dailyLimit": 1000,
    "dailyRemaining": 958,
    "callsMonth": 856,
    "monthlyLimit": 30000,
    "monthlyRemaining": 29144,
    "effectiveRemaining": 958,
    "effectiveLimitScope": "daily",
    "lastUsedAt": "2026-05-05T10:30:00",
    "expiresAt": null,
    "nextDailyResetAt": "2026-05-06T00:00:00",
    "nextMonthlyResetAt": "2026-06-01T00:00:00"
  }
}
```
</details>

---

## Advanced Search Syntax

The `keyword` parameter supports powerful query expressions:

| Syntax | Example | Description |
|--------|---------|-------------|
| Simple terms | `climate change` | Match all terms (implicit AND) |
| Exact phrase | `"trade war"` | Match the exact phrase |
| Force include | `+bitcoin` | Term must appear |
| Exclude | `-bitcoin` | Term must not appear |
| Boolean AND | `crypto AND ethereum` | Both terms required |
| Boolean OR | `bitcoin OR ethereum` | Either term matches |
| Boolean NOT | `crypto NOT bitcoin` | Exclude a term |
| Grouping | `(bitcoin OR ethereum) AND regulation` | Group sub-expressions |
| Combined | `"artificial intelligence" AND (ethics OR regulation) NOT military` | Complex query |

---

## Error Codes

| Code | Message | Description |
|------|---------|-------------|
| `0` | `success` | Request successful |
| `1001` | Parameter error | Invalid or missing parameter |
| `1002` | Validation error | Request body validation failed |
| `1003` | Not found | Resource does not exist |
| `3001` | API Key required | No API key provided |
| `3002` | API Key invalid | The API key does not exist or is malformed |
| `3003` | API Key disabled | The key has been deactivated |
| `3004` | API Key expired | The key has expired |
| `4001` | Daily quota exceeded | Daily request limit reached. Resets at midnight UTC |
| `4002` | Monthly quota exceeded | Monthly request limit reached. Resets on the 1st |
| `4003` | Rate limit exceeded | Too many requests per second. Slow down |
| `5000` | System error | Internal server error |

**Error response example:**

```json
{
  "code": 3001,
  "message": "需要 API Key",
  "data": null,
  "success": false
}
```

---

## Rate Limits & Quotas

Each API key belongs to a plan that determines its quotas:

| | Free | Pro | Enterprise |
|---|---|---|---|
| Daily requests | 1,000 | 10,000 | Unlimited |
| Monthly requests | 30,000 | 300,000 | Unlimited |
| Rate limit | 1 req/s | 10 req/s | 50 req/s |
| Full article content | No (500 chars) | Yes | Yes |
| Support | Community | Email | Priority |

Use `GET /api/v1/account/quota` to monitor your usage in real time.

> Upgrade your plan at [freenews.site/dashboard](https://freenews.site/dashboard).

---

## Code Examples

### Python

```python
import requests

API_KEY = "YOUR_API_KEY"
BASE_URL = "https://freenews.site"

# Get latest news
response = requests.get(
    f"{BASE_URL}/api/v1/news",
    headers={"X-API-Key": API_KEY},
    params={"pageSize": 10, "language": "en"}
)
data = response.json()

for article in data["data"]["items"]:
    print(f"[{article['source']['code']}] {article['title']}")
    if article.get("aiAnalysis"):
        print(f"  Sentiment: {article['aiAnalysis']['sentimentLabel']}")
```

```python
# Search for articles about AI
response = requests.get(
    f"{BASE_URL}/api/v1/search",
    headers={"X-API-Key": API_KEY},
    params={
        "keyword": "artificial intelligence AND regulation",
        "language": "en",
        "sentiment": "negative",
        "pageSize": 5
    }
)
results = response.json()["data"]
print(f"Found {results['total']} articles")

for item in results["items"]:
    print(f"  [{item['sentimentLabel']}] {item['title']}")
```

### JavaScript / Node.js

```javascript
const API_KEY = "YOUR_API_KEY";
const BASE_URL = "https://freenews.site";

// Get latest news
const response = await fetch(
  `${BASE_URL}/api/v1/news?pageSize=10&language=en`,
  { headers: { "X-API-Key": API_KEY } }
);
const { data } = await response.json();

data.items.forEach(article => {
  console.log(`[${article.source.code}] ${article.title}`);
});
```

```javascript
// Search with POST
const searchResponse = await fetch(`${BASE_URL}/api/v1/search`, {
  method: "POST",
  headers: {
    "X-API-Key": API_KEY,
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    keyword: '"climate change" AND (policy OR regulation)',
    language: "en",
    categories: "politics,environment",
    pageSize: 20
  })
});
const results = await searchResponse.json();
console.log(`Found ${results.data.total} articles`);
```

### cURL

```bash
# Get news sources (no API key needed)
curl "https://freenews.site/api/v1/meta/sources"

# Get news with filters
curl "https://freenews.site/api/v1/news?source=reuters&pageSize=5" \
  -H "X-API-Key: YOUR_API_KEY"

# Search articles
curl "https://freenews.site/api/v1/search?keyword=bitcoin&sentiment=positive" \
  -H "X-API-Key: YOUR_API_KEY"

# Check quota
curl "https://freenews.site/api/v1/account/quota" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

## Links

- **Website:** [freenews.site](https://freenews.site)
- **Register:** [freenews.site/register](https://freenews.site/register)
- **Dashboard & Playground:** [freenews.site/dashboard](https://freenews.site/dashboard)
- **Interactive API Docs:** [freenews.site/doc.html](https://freenews.site/doc.html)

---

## License

This repository contains API documentation only. The FreeNews API service is operated by FreeNews. See [Terms of Service](https://freenews.site/terms) for usage terms.
