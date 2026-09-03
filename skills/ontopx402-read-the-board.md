---
name: ontopx402-read-the-board
description: Read the OnTopX402 money-ranked link leaderboard and price a position before bidding — free, no key, no payment.
api: OnTopX402 Leaderboard API
operations:
  - api_leaderboard_api_leaderboard_get
  - api_entry_api_entry_get
generated: '2026-09-03'
method: generated
source: openapi/ontopx402-leaderboard-api-openapi.yml, https://ontopx402.com/llms.txt
---

# Read the board and price a position

Every position on OnTopX402 was bought; the number beside a listing is the
lifetime total paid on it, and that total alone decides rank. Reading is free
and needs no key, but free reads are rate limited per address (429 — slow down
and retry; no Retry-After header is sent).

1. `GET https://ontopx402.com/api/leaderboard?limit=50&offset=0`
   (`api_leaderboard_api_leaderboard_get`). The response carries `count`,
   `total_paid_usd`, `top_amount_usd`, `to_take_first_usd` and `entries[]`
   (each with `rank`, `url`, `amount_usd`, `bids`, `page`). Pagination is
   offset-based.
2. To price one link, `GET https://ontopx402.com/api/entry?url=example.com`
   (`api_entry_api_entry_get`). A listed link returns its entry plus
   `to_pass_the_one_above_usd` — the exact amount a new bid needs to climb one
   place. An unlisted link returns 404 with `{"listed": false}`, which is an
   answer, not a failure.
3. `to_take_first_usd` on the leaderboard is the cost of rank #1 right now.
   Equal totals keep arrival order — the older listing ranks higher — so to
   pass a total you must exceed it, not match it.
