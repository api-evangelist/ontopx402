---
name: ontopx402-bid-for-rank
description: Buy or raise a position on the OnTopX402 leaderboard by paying a self-priced x402 USDC bid — irreversible once settled.
api: OnTopX402 Bidding API
operations:
  - api_bid_api_bid_post
generated: '2026-09-03'
method: generated
source: openapi/ontopx402-bidding-api-openapi.yml, https://ontopx402.com/llms.txt, https://ontopx402.com/rules
---

# Bid for a rank

The amount you name IS the price of the call — there is no fixed price to look
up, no account and no API key. Minimum $1. **Every bid is final: being outbid
refunds nothing, and removals are not refunded** (see the reversibility block in
`conventions/ontopx402-conventions.yml`). Price the position first with the
read-the-board skill.

1. `POST https://ontopx402.com/api/bid` (`api_bid_api_bid_post`) with `url`
   (links only), `description` (max 255 chars — the most recent bid rewrites
   the listing's description) and `amount` in USD. JSON body and query
   parameters are equivalent; the query form exists for payment tools that
   accept only a URL.
2. The response is `402` with an x402 v2 payment-required object priced at
   exactly your amount, in the body and base64-encoded in the
   `PAYMENT-REQUIRED` header. `accepts[]` offers USDC on Base (`eip155:8453`)
   and Solana; `maxTimeoutSeconds` is 300. A 400 instead means the url,
   description or amount is unusable — nothing was charged.
3. Pay one `accepts[]` entry, then resend the identical request with the
   `X-PAYMENT` header. On 200 the row is written and `X-PAYMENT-RESPONSE`
   carries the settlement receipt with the transaction hash. `rank` is your
   position at settlement; `amount_usd` is the lifetime total on the link
   (bids on the same url stack, they never replace).
4. Replays are safe: resending an already-settled payment returns
   `{"already_credited": true}` with the entry unchanged — a settled
   transaction is never counted twice. Nothing is charged and no row appears
   until settlement; a 402 whose body says payments are unavailable means the
   facilitator is unreachable — nothing was charged, retry later.
