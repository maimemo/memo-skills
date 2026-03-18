# Interpretations API (释义)

CRUD for custom word definitions. All scoped to the authenticated user.

## Endpoints

### GET /interpretations — List interpretations

| Param | In | Required | Description |
|-------|----|----------|-------------|
| `voc_id` | query | yes | Word id |

**Response**: `{ "interpretations": [Interpretation, ...] }`

```bash
curl -s "https://open.maimemo.com/open/api/v1/interpretations?voc_id=VOC_ID" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN"
```

### POST /interpretations — Create interpretation

**Body**:
```json
{
  "interpretation": {
    "voc_id": "VOC_ID",
    "interpretation": "n. 苹果",
    "tags": ["考研"],
    "status": "PUBLISHED"
  }
}
```

**Response**: `{ "interpretation": Interpretation }`

```bash
curl -s -X POST "https://open.maimemo.com/open/api/v1/interpretations" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"interpretation":{"voc_id":"VOC_ID","interpretation":"n. 苹果","tags":["考研"],"status":"PUBLISHED"}}'
```

### POST /interpretations/{id} — Update interpretation

**Body**:
```json
{
  "interpretation": {
    "interpretation": "n. 苹果; 苹果公司",
    "tags": ["考研"],
    "status": "PUBLISHED"
  }
}
```

**Response**: `{ "interpretation": Interpretation }`

### DELETE /interpretations/{id} — Delete interpretation

No body. No response body.

## InterpretationStatus Enum

| Value | Description |
|-------|-------------|
| `PUBLISHED` | 发布 |
| `UNPUBLISHED` | 未发布 |
| `DELETED` | 删除 |

## Interpretation Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | yes | Interpretation id |
| `interpretation` | string | yes | Definition text |
| `tags` | string[] | yes | Tags |
| `status` | InterpretationStatus | yes | Status |
| `created_time` | ISO 8601 | yes | Created at |
| `updated_time` | ISO 8601 | yes | Updated at |
