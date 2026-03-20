---
name: memo-api
description: >
  墨墨背单词 MaiMemo Open API skill for vocabulary learning.
  Handles: 单词 vocabulary lookup (voc_id), 释义 interpretations (CRUD),
  助记 mnemonics/notes, 云词本 notepads/word lists, 例句 phrases/example sentences,
  学习数据 study progress/review schedule/study records.
  Triggers on: 墨墨, maimemo, 背单词, 释义, 助记, 云词本, 例句, study progress,
  review schedule, words due, forgotten words, study time, export study data.
metadata:
  openclaw:
    requires:
      env:
        - MAIMEMO_TOKEN
      binds:
        - curl
---

# MaiMemo Open API

## Auth & Base

- **Base URL**: `https://open.maimemo.com/open/api/v1`
- **Token**: env var `$MAIMEMO_TOKEN` — set via app (墨墨背单词 → 开放 API)
- **Auth header**: `Authorization: Bearer $MAIMEMO_TOKEN`

```bash
curl -s -X ${METHOD} "${BASE}/${PATH}" \
  -H "Authorization: Bearer $MAIMEMO_TOKEN" \
  -H "Content-Type: application/json" \
  -d '${BODY}'
```

**Rate limits**: 20/10s, 40/60s, 2000/5h

## Key Concepts

- **voc_id resolution**: Most endpoints need `voc_id`, not spelling. Resolve via `GET /vocabulary?spelling=word` or `POST /vocabulary/query` (batch up to 1000).
- **POST for updates**: Update endpoints use `POST /resource/{id}`, not PUT/PATCH.
- **Status values**: Resources use status enums (`PUBLISHED`, `UNPUBLISHED`, `DELETED`) — vary by domain.
- **Timestamps**: All times are ISO 8601. Study date filters use Beijing timezone (UTC+8).

## Domain Routing

| Task | Read reference file |
|------|-------------------|
| Look up voc_id / spelling → id | `vocabulary-api.md` |
| CRUD custom definitions | `interpretations-api.md` |
| CRUD mnemonics (联想/谐音/派生/词根/词源/固搭/语法/对比 etc.) | `notes-api.md` |
| Manage word lists / cloud notepads | `notepads-api.md` |
| CRUD example sentences | `phrases-api.md` |
| Study progress, records, schedules | `study-api.md` |

**Before calling any endpoint**, read the corresponding reference file for exact params, body schema, and curl examples.

## Study Usage Scenes

All study endpoints are **POST** and **beta** (需要打开自动同步).

### Today's Progress
**Endpoint**: `POST /study/get_study_progress` (empty body)
Returns: `finished` (done count), `total` (target count), `study_time` (ms)

| Scene | Endpoint | Key params |
|-------|----------|-----------|
| Words left today | `get_study_progress` | compute `total - finished` |
| Study time today | `get_study_progress` | `study_time` (ms → min) |

### Today's Words
**Endpoint**: `POST /study/get_today_items`

| Scene | Endpoint | Key params |
|-------|----------|-----------|
| Forgotten words today | `get_today_items` | `is_finished: true`, filter `first_response = "FORGET"` |
| New words today | `get_today_items` | `is_new: true` |
| Unfinished words | `get_today_items` | `is_finished: false` |
| Specific word history | `get_today_items` | `spellings: ["word"]` or `voc_ids: ["id"]` |

### Study Records
**Endpoint**: `POST /study/query_study_records`

| Scene | Endpoint | Key params |
|-------|----------|-----------|
| Words due in next N days | `query_study_records` | `next_study_date.end: "YYYY-MM-DDT00:00:00+08:00"`, `as_count: true` |
| Total words in plan | `query_study_records` | `as_count: true` (no filters) |
| Frequently forgotten (sticky) | `query_study_records` | iterate pages, filter `tags = "STICKING"` |
| Well-familiar words | `query_study_records` | iterate pages, filter `tags = "WELL_FAMILIAR"` |
| Export all study data | `query_study_records` | paginate via sliding `next_study_date` window, `limit: 1000` |

## Multi-domain Workflow Hints

- **Add mnemonic to word**: Vocabulary (get voc_id) → Notes (create)
- **Add example sentence**: Vocabulary (get voc_id) → Phrases (create)
- **Add custom definition**: Vocabulary (get voc_id) → Interpretations (create)
- **Build word list from study data**: Study records (query) → Notepads (create with spellings)
- **Full word info**: Vocabulary (get voc_id) → Interpretations + Notes + Phrases (list all)
