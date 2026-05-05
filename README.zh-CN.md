<p align="center">
  <a href="README.md">English</a>
</p>

<p align="center">
  <h1 align="center">FreeNews API</h1>
  <p align="center">
    全球新闻聚合 API，AI 驱动的智能分析<br/>
    50+ 新闻源 &bull; 20 种语言 &bull; 情感分析与实体提取 &bull; 全文检索
  </p>
  <p align="center">
    <a href="https://freenews.site"><strong>官网</strong></a> &nbsp;&middot;&nbsp;
    <a href="https://freenews.site/register"><strong>免费注册</strong></a> &nbsp;&middot;&nbsp;
    <a href="https://freenews.site/dashboard"><strong>控制台</strong></a>
  </p>
</p>

---

## FreeNews 是什么？

FreeNews 从全球 **50+ 主流新闻源**（BBC、CNN、路透社、美联社等）采集新闻，通过 **AI 分析**（摘要生成、实体提取、情感评分、中英双语翻译）进行结构化处理，最终以统一的 REST API 提供给开发者。

**应用场景：**

- 构建新闻阅读器或资讯看板
- 监测品牌或话题的媒体情绪
- 为 LLM / RAG 管道提供结构化新闻数据
- 全球媒体报道的学术研究
- 内容策展与推荐系统

**核心亮点：**

| 特性 | 说明 |
|------|------|
| **50+ 新闻源** | BBC、CNN、路透社、美联社、NHK、法国世界报等，覆盖 6 大洲 |
| **AI 分析** | 中英双语摘要、关键词提取、实体识别、情感评分、重要性评级 |
| **全文检索** | 基于 Elasticsearch，支持高级布尔查询语法 |
| **20 种语言** | 英语、中文、日语、韩语、法语、德语、西班牙语、阿拉伯语等 |
| **免费套餐** | 注册即可使用，免费额度充足 |

---

## 快速开始

30 秒内获取你的第一条 API 响应：

### 1. 获取 API Key

前往 [freenews.site/register](https://freenews.site/register) 注册账号，在 [控制台](https://freenews.site/dashboard) 创建 API Key。

### 2. 发起第一个请求

```bash
curl "https://freenews.site/api/v1/news?pageSize=5" \
  -H "X-API-Key: YOUR_API_KEY"
```

### 3. 查看返回数据

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
  },
  "success": true
}
```

---

## 认证方式

`/api/v1/news`、`/api/v1/search`、`/api/v1/account` 下的接口需要 API Key。

支持两种传递方式：

| 方式 | 示例 |
|------|------|
| **请求头**（推荐） | `X-API-Key: fn_a1b2c3d4...` |
| **查询参数** | `?api_key=fn_a1b2c3d4...` |

元数据接口（`/api/v1/meta/*`）为**公开接口**，无需 API Key。

---

## 响应格式

所有接口返回统一的 JSON 结构：

```json
{
  "code": 0,
  "message": "success",
  "data": { ... },
  "success": true
}
```

- `code` = `0` 表示成功，`success` = `true`。
- `code` 非零表示错误（`success` = `false`），详见[错误码](#错误码)。
- HTTP 状态码通常为 `200`，即使业务出错也是如此，请始终检查 `code` 字段。

---

## API 参考

**基础地址：** `https://freenews.site`

### 元数据接口（无需 API Key）

#### `GET /api/v1/meta/sources`

获取所有可用的新闻来源列表。

<details>
<summary>响应示例</summary>

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

获取所有支持的语言及对应文章数量。

<details>
<summary>响应示例</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": [
    { "code": "en", "name": "English", "count": 67393 },
    { "code": "zh", "name": "中文", "count": 16545 },
    { "code": "ja", "name": "日本語", "count": 12956 }
  ]
}
```
</details>

---

#### `GET /api/v1/meta/stats`

获取平台整体统计数据。

<details>
<summary>响应示例</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "totalArticles": 118026,
    "aiAnalyzedArticles": 82887,
    "totalSources": 109,
    "totalLanguages": 10
  }
}
```
</details>

---

#### `GET /api/v1/meta/preview`

返回最新 6 条新闻（仅标题和摘要），适合首页展示。

---

### 新闻接口

#### `GET /api/v1/news`

分页获取新闻列表，支持按来源、语言、时间筛选。

**参数：**

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| `page` | integer | 否 | `1` | 页码，从 1 开始 |
| `pageSize` | integer | 否 | `20` | 每页条数，最大 100 |
| `source` | string | 否 | - | 来源代码（如 `bbc`、`cnn`），可通过 `/meta/sources` 获取 |
| `language` | string | 否 | - | 语言代码（如 `en`、`zh`），可通过 `/meta/languages` 获取 |
| `startTime` | string | 否 | - | 起始时间（UTC，ISO 8601），如 `2026-01-01T00:00:00` |
| `endTime` | string | 否 | - | 截止时间（UTC，ISO 8601），如 `2026-01-31T23:59:59` |

**示例：**

```bash
curl "https://freenews.site/api/v1/news?source=bbc&language=en&pageSize=10" \
  -H "X-API-Key: YOUR_API_KEY"
```

<details>
<summary>响应示例</summary>

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

获取新闻详情，包含 AI 分析结果。

**参数：**

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `id` | integer | 是 | 文章 ID（路径参数） |

**示例：**

```bash
curl "https://freenews.site/api/v1/news/12345" \
  -H "X-API-Key: YOUR_API_KEY"
```

<details>
<summary>响应示例</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 12345,
    "title": "Global Tech Summit Opens in Geneva",
    "summary": "World leaders and tech executives gather...",
    "content": "正文内容（是否完整取决于套餐）...",
    "contentTruncated": true,
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

> **提示：** 免费套餐返回截断正文（前 500 字符），付费套餐可获取完整正文。

---

### 搜索接口

#### `GET /api/v1/search`

基于 Elasticsearch 的全文检索，支持高级查询语法。

**参数：**

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| `keyword` | string | 否 | - | 搜索关键词，支持[高级语法](#高级搜索语法) |
| `source` | string | 否 | - | 按来源代码过滤 |
| `language` | string | 否 | - | 按语言代码过滤 |
| `sentiment` | string | 否 | - | 按情感过滤：`positive`、`neutral`、`negative`、`mixed` |
| `categories` | string | 否 | - | 按分类过滤（逗号分隔），如 `technology,politics` |
| `searchIn` | string | 否 | 全部字段 | 限定搜索范围：`title`、`content`、`summary`、`keywords`（逗号分隔） |
| `startDate` | string | 否 | - | 起始日期（UTC，ISO 格式），如 `2026-01-01` |
| `endDate` | string | 否 | - | 截止日期（UTC，ISO 格式），如 `2026-12-31` |
| `page` | integer | 否 | `1` | 页码 |
| `pageSize` | integer | 否 | `20` | 每页条数，最大 100 |

**示例：**

```bash
curl "https://freenews.site/api/v1/search?keyword=人工智能&language=zh&pageSize=5" \
  -H "X-API-Key: YOUR_API_KEY"
```

<details>
<summary>响应示例</summary>

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

与 GET 搜索功能相同，但通过 JSON Body 传参，适合复杂查询。

**请求体：**

```json
{
  "keyword": "\"贸易战\" AND (关税 OR 制裁)",
  "language": "zh",
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

搜索建议（自动补全），**不消耗配额**。

**参数：**

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| `q` | string | 是 | - | 搜索前缀（最多 100 个字符） |
| `limit` | integer | 否 | `10` | 最大建议数（1-50） |

**示例：**

```bash
curl "https://freenews.site/api/v1/search/suggest?q=人工&limit=5" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

### 账户接口

#### `GET /api/v1/account/quota`

查询当前 API Key 的配额和使用量，**不消耗配额**。

**示例：**

```bash
curl "https://freenews.site/api/v1/account/quota" \
  -H "X-API-Key: YOUR_API_KEY"
```

<details>
<summary>响应示例</summary>

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "keyName": "My App Key",
    "keyPrefix": "fn_a1b2...x9z0",
    "status": "active",
    "planName": "free",
    "planDisplayName": "免费版",
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

## 高级搜索语法

`keyword` 参数支持以下查询表达式：

| 语法 | 示例 | 说明 |
|------|------|------|
| 简单词组 | `气候变化` | 匹配所有词（隐含 AND） |
| 精确短语 | `"贸易战"` | 精确匹配完整短语 |
| 强制包含 | `+比特币` | 必须包含该词 |
| 排除 | `-比特币` | 排除包含该词的结果 |
| 布尔 AND | `加密 AND 以太坊` | 两个词都必须出现 |
| 布尔 OR | `比特币 OR 以太坊` | 匹配任意一个 |
| 布尔 NOT | `加密 NOT 比特币` | 排除某个词 |
| 分组 | `(比特币 OR 以太坊) AND 监管` | 用括号分组子表达式 |
| 组合查询 | `"人工智能" AND (伦理 OR 监管) NOT 军事` | 复杂组合查询 |

---

## 错误码

| 错误码 | 说明 | 描述 |
|--------|------|------|
| `0` | 成功 | 请求成功 |
| `1001` | 参数错误 | 参数缺失或格式不正确 |
| `1002` | 验证失败 | 请求体校验不通过 |
| `1003` | 资源不存在 | 请求的资源未找到 |
| `3001` | 需要 API Key | 未提供 API Key |
| `3002` | API Key 无效 | Key 不存在或格式错误 |
| `3003` | API Key 已禁用 | Key 已被停用 |
| `3004` | API Key 已过期 | Key 已过有效期 |
| `4001` | 超出每日配额 | 当日请求次数已用完，UTC 零点重置 |
| `4002` | 超出每月配额 | 当月请求次数已用完，每月 1 日重置 |
| `4003` | 超出速率限制 | 每秒请求过多，请降低频率 |
| `5000` | 系统错误 | 服务端内部错误 |

**错误响应示例：**

```json
{
  "code": 4001,
  "message": "超出每日配额",
  "data": null,
  "success": false
}
```

---

## 配额与限速

每个 API Key 归属一个套餐，套餐决定配额上限：

| | 免费版 | 专业版 | 企业版 |
|---|---|---|---|
| 每日请求 | 1,000 | 10,000 | 不限 |
| 每月请求 | 30,000 | 300,000 | 不限 |
| 速率限制 | 1 次/秒 | 10 次/秒 | 50 次/秒 |
| 完整正文 | 否（500 字符） | 是 | 是 |
| 技术支持 | 社区 | 邮件 | 优先响应 |

通过 `GET /api/v1/account/quota` 实时查看用量。

> 升级套餐请前往 [freenews.site/dashboard](https://freenews.site/dashboard)。

---

## 代码示例

### Python

```python
import requests

API_KEY = "YOUR_API_KEY"
BASE_URL = "https://freenews.site"

# 获取最新新闻
response = requests.get(
    f"{BASE_URL}/api/v1/news",
    headers={"X-API-Key": API_KEY},
    params={"pageSize": 10, "language": "zh"}
)
data = response.json()

for article in data["data"]["items"]:
    print(f"[{article['source']['code']}] {article['title']}")
    if article.get("aiAnalysis"):
        print(f"  情感: {article['aiAnalysis']['sentimentLabel']}")
```

```python
# 搜索关于 AI 的文章
response = requests.get(
    f"{BASE_URL}/api/v1/search",
    headers={"X-API-Key": API_KEY},
    params={
        "keyword": "人工智能 AND 监管",
        "language": "zh",
        "pageSize": 5
    }
)
results = response.json()["data"]
print(f"找到 {results['total']} 篇文章")

for item in results["items"]:
    print(f"  [{item['sentimentLabel']}] {item['title']}")
```

### JavaScript / Node.js

```javascript
const API_KEY = "YOUR_API_KEY";
const BASE_URL = "https://freenews.site";

// 获取最新新闻
const response = await fetch(
  `${BASE_URL}/api/v1/news?pageSize=10&language=zh`,
  { headers: { "X-API-Key": API_KEY } }
);
const { data } = await response.json();

data.items.forEach(article => {
  console.log(`[${article.source.code}] ${article.title}`);
});
```

```javascript
// 使用 POST 搜索
const searchResponse = await fetch(`${BASE_URL}/api/v1/search`, {
  method: "POST",
  headers: {
    "X-API-Key": API_KEY,
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    keyword: '"气候变化" AND (政策 OR 法规)',
    language: "zh",
    categories: "politics,environment",
    pageSize: 20
  })
});
const results = await searchResponse.json();
console.log(`找到 ${results.data.total} 篇文章`);
```

### cURL

```bash
# 获取新闻来源列表（无需 API Key）
curl "https://freenews.site/api/v1/meta/sources"

# 按条件获取新闻
curl "https://freenews.site/api/v1/news?source=bbc&pageSize=5" \
  -H "X-API-Key: YOUR_API_KEY"

# 搜索文章
curl "https://freenews.site/api/v1/search?keyword=比特币&sentiment=positive" \
  -H "X-API-Key: YOUR_API_KEY"

# 查看配额
curl "https://freenews.site/api/v1/account/quota" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

## 相关链接

- **官网：** [freenews.site](https://freenews.site)
- **注册：** [freenews.site/register](https://freenews.site/register)
- **控制台：** [freenews.site/dashboard](https://freenews.site/dashboard)
- **在线 API 文档：** [freenews.site/doc.html](https://freenews.site/doc.html)

---

## 许可协议

本仓库仅包含 API 文档。FreeNews API 服务由 FreeNews 运营，使用条款详见[服务协议](https://freenews.site/terms)。
