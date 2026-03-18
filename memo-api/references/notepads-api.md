# Notepads API (云词本)

CRUD for cloud word lists. Supports pagination, chapters, and bulk word import.

## Endpoints

### GET /notepads — List notepads

| Param | In | Required | Description |
|-------|----|----------|-------------|
| `limit` | query | yes | Page size |
| `offset` | query | yes | Skip count |
| `ids` | query | no | Filter by notepad ids |

**Response**: `{ "notepads": [BriefNotepad, ...] }`

```bash
curl -s "https://open.maimemo.com/open/api/v1/notepads?limit=10&offset=0" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN"
```

### GET /notepads/{id} — Get notepad (full)

Returns full notepad with `content` and parsed `list`.

**Response**: `{ "notepad": Notepad }`

```bash
curl -s "https://open.maimemo.com/open/api/v1/notepads/NOTEPAD_ID" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN"
```

### POST /notepads — Create notepad

**Body**:
```json
{
  "notepad": {
    "title": "GRE高频词",
    "brief": "GRE常考词汇",
    "content": "# Chapter 1\napple\nbanana\n# Chapter 2\ncat\ndog",
    "tags": ["GRE"],
    "status": "PUBLISHED"
  }
}
```

**Response**: `{ "notepad": Notepad }`

```bash
curl -s -X POST "https://open.maimemo.com/open/api/v1/notepads" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"notepad":{"title":"GRE高频词","brief":"GRE常考词汇","content":"# Chapter 1\napple\nbanana","tags":["GRE"],"status":"PUBLISHED"}}'
```

### POST /notepads/{id} — Update notepad

Same body structure as create. All fields required.

**Response**: `{ "notepad": Notepad }`

### DELETE /notepads/{id} — Delete notepad

**Response**: `{ "notepad": Notepad }`

## Content Format

- One word per line
- Lines starting with `#` are chapter headings
- Example: `# Chapter 1\napple\nbanana\n# Chapter 2\ncat`

## NotepadStatus Enum

| Value | Description |
|-------|-------------|
| `PUBLISHED` | 发布 |
| `UNPUBLISHED` | 未发布 |
| `DELETED` | 删除 |

## NotepadType Enum

| Value | Description |
|-------|-------------|
| `FAVORITE` | 我的收藏 |
| `NOTEPAD` | 云词本 |

## BriefNotepad Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | yes | Notepad id |
| `type` | NotepadType | yes | Type |
| `creator` | integer | yes | Creator user id |
| `status` | NotepadStatus | yes | Status |
| `title` | string | yes | Title |
| `brief` | string | yes | Description |
| `tags` | string[] | yes | Tags |
| `created_time` | ISO 8601 | yes | Created at |
| `updated_time` | ISO 8601 | yes | Updated at |

## Notepad Schema (extends BriefNotepad)

Additional fields:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `content` | string | yes | Raw content (newline-separated words, `#` chapters) |
| `list` | NotepadParsedItem[] | yes | Parsed content |

### NotepadParsedItem

| Field | Type | Description |
|-------|------|-------------|
| `type` | `"CHAPTER"` \| `"WORD"` | Item type |
| `data.chapter` | string | Chapter name (always present) |
| `data.word` | string | Word spelling (only when type=WORD) |
