# Notes API (助记)

CRUD for word mnemonics. All scoped to the authenticated user.

## Endpoints

### GET /notes — List notes

| Param | In | Required | Description |
|-------|----|----------|-------------|
| `voc_id` | query | yes | Word id |

**Response**: `{ "notes": [Note, ...] }`

```bash
curl -s "https://open.maimemo.com/open/api/v1/notes?voc_id=VOC_ID" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN"
```

### POST /notes — Create note

**Body**:
```json
{
  "note": {
    "voc_id": "VOC_ID",
    "note_type": "谐音",
    "note": "apple 谐音 '阿婆' → 阿婆在吃苹果"
  }
}
```

**Response**: `{ "note": Note }`

```bash
curl -s -X POST "https://open.maimemo.com/open/api/v1/notes" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"note":{"voc_id":"VOC_ID","note_type":"谐音","note":"apple 谐音 阿婆 → 阿婆在吃苹果"}}'
```

### POST /notes/{id} — Update note

**Body**:
```json
{
  "note": {
    "note_type": "词根",
    "note": "updated mnemonic text"
  }
}
```

**Response**: `{ "note": Note }`

### DELETE /notes/{id} — Delete note

No body. No response body.

## note_type Values

`联想` · `谐音` · `派生` · `词根` · `词源` · `固搭` · `语法` · `对比` · `近义` · `反义` · `扩展` · `串记` · `口诀` · `合成` · `吐槽` · `其他` · `固定搭配` · `词根词缀` · `辨析` · `近反义词` · `图例`

## NoteStatus Enum

| Value | Description |
|-------|-------------|
| `PUBLISHED` | 发布 |
| `DELETED` | 删除 |

## Note Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | yes | Note id |
| `note_type` | string | yes | Mnemonic type |
| `note` | string | yes | Mnemonic content |
| `status` | NoteStatus | yes | Status |
| `created_time` | ISO 8601 | yes | Created at |
| `updated_time` | ISO 8601 | yes | Updated at |
