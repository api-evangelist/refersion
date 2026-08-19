---
name: refersion-onboard-affiliate
description: Onboard a new affiliate into a Refersion program end to end — create the account, attach a coupon or email conversion trigger, and confirm the referral link — using the Refersion REST API v2.
api: refersion:refersion-affiliates-api
generated: '2026-08-13'
method: generated
source: openapi/_original/refersion-rest-api-readme-harvest.json + https://www.refersion.dev/reference/new_affiliate
operations:
  - new_affiliate
  - new_affiliate_trigger
  - get_affiliate
  - affiliate_status_change
---

# Onboard an affiliate

Base URL `https://api.refersion.com/v2`. Every call is a **POST** with
`Content-Type: application/json` and both credential headers:

```
Refersion-Public-Key: <public key>
Refersion-Secret-Key: <secret key>
```

Keys come from Account > Settings > Refersion API. There are no scopes — these keys grant the
entire API surface.

## Step 1 — create the affiliate (`new_affiliate`, `POST /affiliate/new`)

Required: `first_name`, `last_name`, `email`, `password`, `status`.

`status` must be `PENDING` or `ACTIVE`. `password` must be 9–72 characters with at least one
number, one uppercase letter, one lowercase letter and one symbol, must not equal the email, and
must not repeat any character more than three times in a row — the API rejects violations with a
422 listing each rule it failed.

Useful optional fields: `offer` (offer ID; the account default is used when omitted),
`send_welcome` (`TRUE`/`FALSE`, default `FALSE`), `conversion_trigger_coupon` (creates a coupon
trigger inline, which can replace step 2), `unique_merchant_id` (your own identifier for this
affiliate), `country` (two-letter code from the allow-list in the spec).

**Save both values from the 200 response.** `id` carries the referral code and `link` is the
affiliate's referral URL (`https://<site>.refersion.com/c/<code>`). The `message` field states the
numeric account ID. The docs explicitly recommend persisting the identifier so you can map your
user to their Refersion account later — you will need the *code* for triggers and the *numeric ID*
for status changes and credits.

## Step 2 — attach a conversion trigger (`new_affiliate_trigger`, `POST /affiliate/trigger`)

Skip this if you passed `conversion_trigger_coupon` in step 1.

Required: `affiliate_code` (the code from step 1, not the numeric ID), `type`, `trigger`.

`type` is `COUPON`, `SKU` or `EMAIL`. Trigger values are globally unique across the whole account —
reusing one returns 422 `"Conversion trigger already exists for same or another affiliate."` Treat
that as "already onboarded", not as a retryable error.

## Step 3 — verify (`get_affiliate`, `POST /affiliate/get`)

Send either the numeric `id` or the referral `code`. A 422 `"Affiliate not found."` means the
create did not take effect; a 422 `"Request filter not provided."` means neither identifier was
sent.

## Step 4 — activate if you created it PENDING (`affiliate_status_change`, `POST /affiliate/status_change`)

Required: `ids` (array of numeric affiliate IDs, **maximum 50 per call**) and `status`
(`ACTIVE`, `DENIED` or `DISABLED`).

This returns **HTTP 200 even when some IDs fail**. Read `ids_changed` and `ids_not_changed` and
reconcile — the status code alone tells you nothing, and no per-ID reason is returned.

## Rules that apply to every step

- **There is no idempotency key.** Re-running step 1 after a timeout creates a second affiliate.
  Search by email with `search_affiliates` before retrying.
- **HTTP 204 is a failure**, not a success — Refersion returns 204 with `{"error": "Invalid field X"}`.
  Most HTTP clients discard a 204 body, so check for the status explicitly.
- **HTTP 404 means "empty request body"**, not "not found". Always send a JSON body.
- 401 is always `{"error": "Invalid API credentials (error 2)."}` — check both headers.
- Full error catalog: `errors/refersion-problem-types.yml`. Conventions: `conventions/refersion-conventions.yml`.
