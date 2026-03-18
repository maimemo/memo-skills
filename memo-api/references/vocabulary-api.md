# Vocabulary API

Read-only endpoints to resolve `voc_id` from spelling (or vice versa). Most other APIs require `voc_id`.

## Endpoints

### GET /vocabulary — Get single word

| Param | In | Required | Description |
|-------|----|----------|-------------|
| `spelling` | query | yes | Word spelling (e.g. `apple`) |

**Response**: `{ "voc": { "id": "...", "spelling": "..." } }`

```bash
curl -s "https://open.maimemo.com/open/api/v1/vocabulary?spelling=apple" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN"
```

### POST /vocabulary/query — Batch query

Query by spellings or ids (mutually exclusive, max 1000 each).

**Body**:
```json
{ "spellings": ["apple", "banana"] }
// or
{ "ids": ["id1", "id2"] }
```

**Response**: `{ "voc": [{ "id": "...", "spelling": "..." }, ...] }`

```bash
curl -s -X POST "https://open.maimemo.com/open/api/v1/vocabulary/query" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"spellings": ["apple", "banana"]}'
```

## Vocabulary Schema

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique voc_id |
| `spelling` | string | Word spelling |
