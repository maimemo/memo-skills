# Study API (学习数据)

**Beta (公测)**: 限时试用，不保证可用性，可能随时调整。需要打开自动同步。如果当日未打开 App 进行初始化则无法准确计算。

## Endpoints

### POST /study/get_study_progress — Today's progress

No request body needed.

**Response**:
```json
{
  "progress": {
    "finished": 10,
    "total": 20,
    "study_time": 114514
  }
}
```

```bash
curl -s -X POST "https://open.maimemo.com/open/api/v1/study/get_study_progress" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN"
```

### POST /study/get_today_items — Today's word list

**Body params** (all optional):

| Field | Type | Description |
|-------|------|-------------|
| `is_finished` | boolean | Filter by completion status |
| `is_new` | boolean | Filter new words only |
| `voc_ids` | string[] | Query by voc_ids (max 1000, ignores other filters) |
| `spellings` | string[] | Query by spellings (max 1000, ignores other filters, mutually exclusive with voc_ids) |
| `limit` | integer | Max results by study order (default 50, max 1000) |

**Response**: `{ "today_items": [StudyTodayItem, ...] }`

```bash
# Unfinished words
curl -s -X POST "https://open.maimemo.com/open/api/v1/study/get_today_items" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"is_finished": false, "limit": 1000}'
```

### POST /study/query_study_records — Query study records

**Body params** (all optional):

| Field | Type | Description |
|-------|------|-------------|
| `next_study_date.start` | ISO 8601 | Filter ≥ date (Beijing timezone) |
| `next_study_date.end` | ISO 8601 | Filter ≤ date (Beijing timezone) |
| `voc_ids` | string[] | Query by voc_ids (ignores other filters) |
| `spellings` | string[] | Query by spellings (max 1000, ignores other filters, mutually exclusive with voc_ids) |
| `as_count` | boolean | Return count only, no data list |
| `limit` | integer | Max results by next_study_date order (default 50, max 1000) |

**Response**: `{ "records": [StudyRecord, ...], "count": 114514 }`

- When `as_count=true`: `records` is empty, `count` has the total.
- When `as_count=false`: `records` has data, `count` is 0.

```bash
# Total words in plan
curl -s -X POST "https://open.maimemo.com/open/api/v1/study/query_study_records" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"as_count": true}'
```

## Usage Scenes

### 1. Words left today / study time
```bash
curl -s -X POST ".../study/get_study_progress" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN"
# → total - finished = remaining; study_time / 60000 = minutes
```

### 2. Forgotten words today
```bash
curl -s -X POST ".../study/get_today_items" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"is_finished": true, "limit": 1000}'
# → filter items where first_response = "FORGET"
```

### 3. New words today
```bash
curl -s -X POST ".../study/get_today_items" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"is_new": true, "limit": 1000}'
```

### 4. Unfinished words
```bash
curl -s -X POST ".../study/get_today_items" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"is_finished": false, "limit": 1000}'
```

### 5. Specific word study history
```bash
curl -s -X POST ".../study/get_today_items" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"spellings": ["apple"]}'
# For full history use query_study_records:
curl -s -X POST ".../study/query_study_records" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"spellings": ["apple"]}'
```

### 6. Words due in next N days
```bash
# Example: words due by April 1st
curl -s -X POST ".../study/query_study_records" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"next_study_date": {"end": "2026-04-01T00:00:00+08:00"}, "as_count": true}'
```

### 7. Total words in plan
```bash
curl -s -X POST ".../study/query_study_records" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"as_count": true}'
```

### 8. Frequently forgotten words (sticky)
```bash
# Paginate with sliding date window, filter tags = "STICKING"
curl -s -X POST ".../study/query_study_records" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"limit": 1000}'
# → filter records where tags = "STICKING"
```

### 9. Well-familiar words
```bash
curl -s -X POST ".../study/query_study_records" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"limit": 1000}'
# → filter records where tags = "WELL_FAMILIAR"
```

### 10. Export all study data
Paginate via sliding `next_study_date` window since API returns max 1000 per call:
```bash
# Page 1: earliest records
curl -s -X POST ".../study/query_study_records" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"limit": 1000}'
# Page 2+: use last record's next_study_date as new start
# -d '{"next_study_date": {"start": "LAST_RECORD_DATE"}, "limit": 1000}'
# Repeat until fewer than 1000 records returned.
```

### 11. Words due on a specific date range
```bash
curl -s -X POST ".../study/query_study_records" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"next_study_date": {"start": "2026-03-20T00:00:00+08:00", "end": "2026-03-25T00:00:00+08:00"}, "limit": 1000}'
```

### 12. Words due on a date
```bash
curl -s -X POST ".../study/query_study_records" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"next_study_date": {"end": "2026-03-25T00:00:00+08:00"}, "limit": 1000}'
```

## StudyResponse Enum

| Value | Description |
|-------|-------------|
| `FAMILIAR` | 认识 |
| `VAGUE` | 模糊 |
| `FORGET` | 忘记 |
| `WELL_FAMILIAR` | 熟知 |
| `CANCEL_WELL_FAMILIAR` | 取消熟知 |

## StudyProgress Schema

| Field | Type | Description |
|-------|------|-------------|
| `finished` | integer | Words completed today |
| `total` | integer | Target word count today |
| `study_time` | integer | Study time in milliseconds |

## StudyTodayItem Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `voc_id` | string | yes | Word id |
| `voc_spelling` | string | yes | Word spelling |
| `order` | integer | yes | Study order |
| `first_response` | StudyResponse | no | First response today |
| `is_new` | boolean | yes | Is new word |
| `is_finished` | boolean | yes | Finished today |

## StudyRecord Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `voc_id` | string | yes | Word id |
| `voc_spelling` | string | yes | Word spelling |
| `add_date` | ISO 8601 | yes | Date added |
| `first_study_date` | ISO 8601 | no | First study date |
| `last_study_date` | ISO 8601 | no | Last study date |
| `next_study_date` | ISO 8601 | no | Next study date |
| `last_response` | StudyResponse | no | Last response |
| `study_count` | integer | yes | Study count (max 1/day) |
| `tags` | `"STICKING"` \| `"WELL_FAMILIAR"` | yes | Record tag |
