# Phrases API (例句)

CRUD for example sentences. All scoped to the authenticated user.

## Endpoints

### GET /phrases — List phrases

| Param | In | Required | Description |
|-------|----|----------|-------------|
| `voc_id` | query | yes | Word id |

**Response**: `{ "phrases": [Phrase, ...] }`

```bash
curl -s "https://open.maimemo.com/open/api/v1/phrases?voc_id=VOC_ID" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN"
```

### POST /phrases — Create phrase

**Body**:
```json
{
  "phrase": {
    "voc_id": "VOC_ID",
    "phrase": "This is an apple.",
    "interpretation": "这是一个苹果。",
    "tags": ["词典"],
    "origin": "自编"
  }
}
```

**Response**: `{ "phrase": Phrase }`

```bash
curl -s -X POST "https://open.maimemo.com/open/api/v1/phrases" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"phrase":{"voc_id":"VOC_ID","phrase":"This is an apple.","interpretation":"这是一个苹果。","tags":["词典"],"origin":"自编"}}'
```

### POST /phrases/{id} — Update phrase

**Body**:
```json
{
  "phrase": {
    "phrase": "I ate an apple.",
    "interpretation": "我吃了一个苹果。",
    "tags": ["词典"],
    "origin": "自编"
  }
}
```

**Response**: `{ "phrase": Phrase }`

### DELETE /phrases/{id} — Delete phrase

**Response**: `{ "phrase": Phrase }`

## Valid Tags (multi-select, max 3)

`小学` · `初中` · `高中` · `四级` · `六级` · `专升本` · `专四` · `专八` · `考研` · `考博` · `雅思` · `托福` · `托业` · `新概念` · `GRE` · `GMAT` · `BEC` · `MBA` · `SAT` · `ACT` · `法学` · `医学` · `词典` · `短语`

## PhraseStatus Enum

| Value | Description |
|-------|-------------|
| `PUBLISHED` | 发布 |
| `DELETED` | 删除 |

## PhraseHighlightRange

Marks the word position in the sentence. Half-open interval `[start, end)`.

| Field | Type | Description |
|-------|------|-------------|
| `start` | integer | Start index |
| `end` | integer | End index (exclusive) |

## Phrase Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | yes | Phrase id |
| `phrase` | string | yes | Sentence text |
| `interpretation` | string | yes | Translation |
| `tags` | string[] | yes | Tags |
| `highlight` | PhraseHighlightRange[] | yes | Word highlight ranges |
| `status` | PhraseStatus | yes | Status |
| `origin` | string | yes | Source/origin |
| `created_time` | ISO 8601 | yes | Created at |
| `updated_time` | ISO 8601 | yes | Updated at |
