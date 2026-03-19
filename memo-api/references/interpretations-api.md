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
    "tags": ["简明"],
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
    "tags": ["简明"],
    "status": "PUBLISHED"
  }
}
```

**Response**: `{ "interpretation": Interpretation }`

### DELETE /interpretations/{id} — Delete interpretation

No body. No response body.

## Valid Tags (multi-select, max 3)

`简明` · `详细` · `英英` · `小学` · `初中` · `高中` · `四级` · `六级` · `专升本` · `专四` · `专八` · `考研` · `考博` · `雅思` · `托福` · `托业` · `新概念` · `GRE` · `GMAT` · `BEC` · `MBA` · `SAT` · `ACT` · `法学` · `医学`

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
