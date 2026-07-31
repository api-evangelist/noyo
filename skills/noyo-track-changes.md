---
name: Track member changes and resolve discrepancies
description: Submit a member snapshot and follow the resulting field-level differences through carrier confirmation, handling any discrepancies via the Tracking API.
api: openapi/noyo-tracking-openapi.json
operations: [createMemberSnapshot, getDifferenceList, getDifference, getEmployeeDifferenceList, dismissDifference]
---

# Track member changes

Use the member snapshot + Tracking API to get field-level transparency from
submission to carrier confirmation.

## Auth
Bearer token from `POST https://accounts.noyo.com/auth/public/token`. The
Tracking API is served from `https://tracking.noyo.com`
(sandbox `https://tracking-sandbox.noyo.com`).

## Steps
1. **Submit desired state** — `createMemberSnapshot` (Fulfillment API) with the
   employee's coverage elections. Noyo emits a `difference.created` event per
   field-level change.
2. **List differences** — `getDifferenceList`, or `getEmployeeDifferenceList` for
   a single employee. Use `getDifference` for full detail including its source.
3. **Handle discrepancies** — a `difference.discrepancy` event signals a carrier
   mismatch; resolve by sending a corrective snapshot or `dismissDifference` when
   appropriate. A `difference.resolved` event confirms closure (differences
   cannot be reopened once resolved).

## Rules
- Webhooks are at-least-once and unordered — deduplicate on `event.id` and refetch
  via REST; verify the `x-noyo-signature` HMAC. See asyncapi/noyo-webhooks.yml.
- Webhooks are production-only (not available in sandbox).
