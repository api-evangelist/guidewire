---
name: guidewire-safe-writes
description: >-
  Write safely to a Guidewire InsuranceSuite Cloud API deployment — duplicate suppression, lost-update
  protection, no-commit rehearsal, correlation tracing, and what can and cannot be taken back.
api: Guidewire InsuranceSuite Cloud API (all APIs)
generated: '2026-09-12'
method: generated
source: >-
  Guidewire InsuranceSuite Cloud API Consumer Guide, "Request headers" and "Lost updates and
  checksums" —
  https://docs.guidewire.com/cloud/cc/202511/cloudapibf/cloudAPI/topics/101-Fund/07-request-headers/c_HTTP-headers.html
grounding: >-
  Every header, default and failure mode below is quoted from Guidewire's public consumer guide.
  Nothing here is derived from the scaffold specs in openapi/.
operations:
  - PATCH /{api}/v1/{resource}/{id}
  - DELETE /claim/v1/claims/{claimId}/checks/{checkId}
  - POST /composite/v1/composite
  - POST /{api}/v1/{resource}
---

# Writing safely to Guidewire Cloud API

## 1. Make the write un-repeatable before you send it

Put a globally unique value in **`GW-DBTransaction-ID`** (string, ≤128 characters) on every call that
commits. Cloud API inserts it into the instance's TransactionID table; if the value is already there
the call is rejected with **HTTP 400 / `gw.api.webservice.exception.AlreadyExecutedException`**.

The value must be unique **across all clients, APIs and web services** on that deployment — scope your
IDs accordingly (a UUID is fine; a per-agent counter is not).

**This is duplicate suppression, not idempotent replay.** Guidewire is explicit: *"Duplicate requests
do not return identical responses. The first request will succeed, but subsequent requests will fail.
It is the responsibility of the caller application to decide how or if to handle this situation."*

So: a 400 with `AlreadyExecutedException` means **the work is done**. GET the resource and confirm.
Never treat it as a retryable error.

Three documented limits: it only applies to calls that commit; only when the call commits exactly once
(multi-commit calls are rare); and only when the commit is the first side effect — if the endpoint
notifies an external system before committing, the notification can still fire twice.

## 2. Do not clobber a value you did not read

Send **`GW-Checksum`** with the checksum you received when you read the resource. The commit proceeds
only if it still matches. Applies to PATCHes, business-action POSTs and DELETEs. On a mismatch:
re-GET, re-apply your change to the fresh state, re-send with the new checksum. Do not strip the header
to force the write through.

## 3. Rehearse without committing

**`GW-DoNotCommit: true`** executes the request and suppresses the commit. Guidewire documents it as an
endpoint warm-up device (loading Java/Gosu classes before real traffic), so the response shape on a
suppressed write is not specified as a validation contract. Useful for proving a payload is accepted;
not a substitute for a real dry-run API, and not something to report to a user as "validated".

## 4. Fail loudly on anything you are not sure about

Unknown request properties and unknown query parameters are **rejected by default**. You can loosen
this per request with `GW-UnknownPropertyHandling` / `GW-UnknownQueryParamHandling`
(`log` | `reject` | `ignore`) — don't. The default is the safe one, and it is the fastest way to learn
that your payload has drifted from this tenant's generated contract.

In PolicyCenter, `GW-FailOnValidationWarnings: true` makes quote / bind-only / bind-and-issue fail on
validation **warnings**, not only errors. On a money-moving flow, prefer the strict setting.

## 5. Trace it

Send **`X-Correlation-ID`**. It is repeatable and it follows the request through every downstream
application. What actually lands in the logs depends on the deployment's `TraceabilityIDPlugin` — the
default uses your value if you send one, otherwise it generates a UID. Send one, and record it beside
whatever you told the user.

## 6. Know what you can take back before you act

| Action | Reversal | Window |
|---|---|---|
| Create a draft claim | `POST /claim/v1/claims/{claimId}/cancel` | **Draft only.** Once submitted it can be closed, never cancelled. |
| Create a claim check | `DELETE /claim/v1/claims/{claimId}/checks/{checkId}` | **Before escalation.** Checks cannot be deleted once escalated. Delete is per check — there is no check-set delete. |
| PATCH any resource | none published | — Read-before-write and keep the prior state yourself. |

## 7. Batch carefully

- `POST /composite/v1/composite` — ordered sub-requests that can reference each other's results.
  Required for some flows (creating a claim on an unverified policy).
- `POST /batch` on the Common API — independent operations in one call.
- `Prefer: respond-async` (optionally `respond-async, wait=T` / `wait-ms=T`) queues the call; collect
  the result from `/async/v1`.

Idempotency and checksums still apply inside a composite. Give the composite call its own
`GW-DBTransaction-ID`.

## 8. What the platform does not give you

- **No published rate limits, no `RateLimit-*` headers, no documented 429 contract.** Throttling is set
  per tenant by the Guidewire Cloud subscription. Back off exponentially and honour `Retry-After`.
- **No RFC 9457 `problem+json`.** Errors are an HTTP status plus a fully-qualified Guidewire exception
  class name. Match on the class name, not on prose.
- **No published deprecation policy or `Sunset` header.** Major versions coexist at `/vN`, but the
  definition of a breaking change lives in a customer-only Schema Backwards Compatibility Contract.
- **A per-tenant contract.** Line-of-business endpoints are generated from that customer's Advanced
  Product Designer flows. Read `<applicationURL>/rest/<APIpath>/openapi.json` from the instance you are
  actually calling; do not assume another tenant's shape.
