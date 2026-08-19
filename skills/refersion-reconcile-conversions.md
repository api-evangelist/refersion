---
name: refersion-reconcile-conversions
description: Review, approve, deny, cancel and manually credit Refersion conversions, and pull aggregate commission totals for a period — the money-moving half of the Refersion REST API v2.
api: refersion:refersion-conversions-api
generated: '2026-08-13'
method: generated
source: openapi/_original/refersion-rest-api-readme-harvest.json + https://www.refersion.dev/reference/status_change
operations:
  - get_totals
  - status_change
  - cancel_conversion
  - manual_commission_credit
  - manual_credit_order_id
---

# Reconcile conversions

Base URL `https://api.refersion.com/v2`. All calls are POST with `Refersion-Public-Key`,
`Refersion-Secret-Key` and `Content-Type: application/json`.

> **This skill moves money.** `manual_commission_credit` and `manual_credit_order_id` create real
> commission liabilities and there is **no idempotency key**. Never retry either one blindly on a
> timeout, and gate both behind human confirmation when an autonomous agent is driving. See
> `agentic-access/refersion-agentic-access.yml`.

## Step 1 — measure the period (`get_totals`, `POST /conversion/totals`)

Required: `status` (array of conversion statuses).

Filters: `created_from` / `created_to` (UTC dates), `offer_id`, `affiliate_id`, `payment_status`,
`type`, `is_test_conversion`.

Returns `conversions_count`, `commission_total`, `order_total`, `commissionable_order_total` and
`currency`. This is an **aggregate only** — the API has no operation that enumerates individual
conversions, so you cannot page a list of conversions to reconcile against. If you need per-order
detail you must have captured it from the New Conversion webhook
(`asyncapi/refersion-webhooks.yml`).

## Step 2 — approve or deny in bulk (`status_change`, `POST /conversion/status_change`)

Required: `ids` (array of conversion IDs) and `status`.

State transitions are enforced server-side and surface as 422:
`"Cannot deny an approved conversion"`, `"Cannot approve a denied conversion"`,
`"The conversion cannot have this status"`. Read the current status before flipping it.

Like every batch operation here, this returns **200 with `ids_changed` / `ids_not_changed`**.
Reconcile against `ids_changed`; a 200 does not mean all IDs moved.

## Step 3 — cancel a referred order (`cancel_conversion`, `POST /conversion/cancel`)

Required: `order_id` (the order ID from your commerce store) and `items` (array of items to cancel —
you may cancel a subset of SKUs from a multi-SKU order).

Returns `{"conversion_id": "..."}` for the cancellation conversion it created. An unknown order
returns 422 `"Invalid order ID (error 5)"`.

## Step 4a — credit an arbitrary amount (`manual_commission_credit`, `POST /conversion/manual_credit`)

Required: `id` (numeric **affiliate** ID), `commission` (may be **negative** to claw back), `status`.
Optional: `currency` (account default when omitted), `notes`.

Failures: 422 `"Invalid affiliate ID (error 2)."`, `"X is not a valid currency"`.

**No server-side deduplication exists on this operation.** Calling it twice credits twice.

## Step 4b — credit against an existing order (`manual_credit_order_id`, `POST /conversion/manual_credit_order_id`)

Required: `id` (numeric affiliate ID) and `order_id`. Optional: `status`, `notes`.

This is the *only* operation in the API with built-in dedupe: a repeat returns 422
`"The affiliate you provided has already been manually credited for this order ID. (Error 6)"`.
Treat that as success-already-applied. A concurrent request returns 422
`"This order ID is being process (error 5)."` — back off and re-check rather than retrying
immediately.

A 200 here can still carry a `warning`, e.g.
`{"message": "Order(s) are re-processing...", "warning": "Order ID is already credited to affiliate 12345678"}`.
Read `warning` on every 200.

## Rules that apply to every step

- **HTTP 204 is a failure** carrying `{"error": "Invalid field X"}`.
- **HTTP 404 means "empty request body"**, not "not found".
- **HTTP 429 here means a payload-size limit** (max 50 elements on an array attribute), not a rate
  limit — retrying after a delay will fail identically. Split the batch instead.
- Full error catalog: `errors/refersion-problem-types.yml`.
