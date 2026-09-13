---
name: guidewire-fnol-claim-intake
description: >-
  File a First Notice of Loss against a Guidewire ClaimCenter deployment over the InsuranceSuite Cloud
  API — create a draft claim, enrich it, submit it, and cancel it while it is still cancellable.
api: Guidewire InsuranceSuite Cloud API — Claim API (/claim/v1)
generated: '2026-09-12'
method: generated
source: >-
  Guidewire ClaimCenter Cloud API Consumer Guide, "Executing FNOL" —
  https://docs.guidewire.com/cloud/cc/202511/cloudapibf/cloudAPI/topics/111-CCFNOL/01-executing-FNOL/c_the-FNOL-process-in-Cloud-API.html
grounding: >-
  Endpoint paths, payload shape and state rules below are quoted from Guidewire's published consumer
  guide. They are NOT taken from the openapi/ files in this repository, which are documentation-shaped
  scaffolds. operationIds are deliberately omitted: Guidewire serves the authoritative definition only
  from a running instance (<applicationURL>/rest/claim/v1/openapi.json), so inventing one here would be
  a fabrication.
operations:
  - POST /claim/v1/claims
  - PATCH /claim/v1/claims/{claimId}
  - POST /claim/v1/claims/{claimId}/submit
  - POST /claim/v1/claims/{claimId}/cancel
  - POST /composite/v1/composite
  - POST /test-util/v1/policies
---

# Filing a First Notice of Loss through Guidewire Cloud API

## Before you start

**There is no public Guidewire API host.** You call the customer's own ClaimCenter instance:
`<applicationURL>/rest/claim/v1/...`. `api.guidewire.com` does not resolve. Confirm the application
URL and a credential with the deployment owner before doing anything else.

Authenticate with a bearer JWT. HTTP Basic exists but Guidewire supports it for internal users in
**development environments only** — never in production.

You cannot create a claim without a policy, and every claim owns its own copy of the policy.

## The minimum flow — always at least two calls

### 1. Create the draft claim

```
POST <applicationURL>/rest/claim/v1/claims
Content-Type: application/json
Authorization: Bearer <jwt>
GW-DBTransaction-ID: <globally-unique-id>

{ "data": { "attributes": {
    "lossDate": "2026-02-01T07:00:00.000Z",
    "policyNumber": "FNOL-POLICY"
} } }
```

Policy number and loss date are the **minimum**. The policy must exist in the system acting as the
Policy Administration System and must be **in force on the loss date** — Cloud API, unlike the New
Claim Wizard, refuses a claim whose verified policy is not in force at the loss date. A bad policy
number or date returns **400** with a `userMessage` like
`"No policy was found with policy number ABC123 for loss date 2020-01-01T07:00:00.000Z"`.

The response carries the claim's `id` **and** its draft claim number. Persist both. ClaimCenter copies
the policy from the PAS into its own policy graph as part of this call.

### 2. Enrich (optional, repeatable)

```
PATCH <applicationURL>/rest/claim/v1/claims/{claimId}
GW-Checksum: <checksum from the last read>
```

Use this when you opened the draft before you had everything. Send `GW-Checksum` so a concurrent
editor cannot be silently clobbered — a mismatch rejects the commit instead of overwriting.

### 3. Submit

```
POST <applicationURL>/rest/claim/v1/claims/{claimId}/submit
GW-DBTransaction-ID: <a different globally-unique-id>
```

Submission promotes the draft to an open claim, assigns an **open** claim number (different from the
draft number), and fires ClaimCenter's automated claim-setup rules — segmentation, assignment, and
activity creation. The response carries the open claim number.

## Undo: what is reversible and when

```
POST <applicationURL>/rest/claim/v1/claims/{claimId}/cancel
```

Cancel **discards** the draft and removes all information about it from the database.

**The window is the draft state and nothing wider.** Guidewire: *"You can cancel only draft claims.
Once a claim has been submitted, it can be closed. But it can no longer be canceled."* After step 3
there is no undo — closing an open claim is a business outcome, not a reversal. If you are unsure
whether a claim should exist, decide before you POST `/submit`.

There is no documented undo for a committed `PATCH`. Read before you write and keep the prior state.

## Policy-state variants

| Policy state | Calls required |
|---|---|
| **Test Util policy** (dev, no PAS) | `POST /test-util/v1/policies`, then the claim POST, then `/submit`. Test Util policies cannot be PATCHed. |
| **Verified policy** (PAS connected) | Claim POST (policy copies from the PAS automatically), then `/submit`. |
| **Unverified policy** | Must go through a composite request: `POST /composite/v1/composite` containing `POST /claim/v1/unverified-policies`, the claim POST, and `/submit`. You typically cannot make payments on a claim whose policy is still unverified, and there is no endpoint to refresh an unverified policy into a verified one. |

## Validation

By default a Cloud API claim must reach the same validation level as the New Claim Wizard: claim,
policy and incidents at **New Loss Completion**; exposures at **Load Save**. The deployment can set
`RequireExposuresToBeNewLossWhenSubmittingWithCloudAPI` to `true` to hold exposures to New Loss
Completion as well. Ask which setting the instance uses — it changes what will be rejected.

## Rules that apply to every call here

- Send `GW-DBTransaction-ID` (≤128 chars, globally unique) on both POSTs. A replay is rejected with
  **400 / `gw.api.webservice.exception.AlreadyExecutedException`**. That means *already applied* —
  reconcile by reading state, do **not** retry.
- Unknown payload properties and unknown query parameters are **rejected by default**. Do not send a
  speculative field.
- Errors are HTTP status plus a Guidewire exception class name. There is no RFC 9457 `problem+json`.
- Collections paginate with `pageSize` (default 25, max 100), `pageOffset`, `includeTotal`; follow the
  returned `next` link rather than building offsets.
- No rate limits are published; throttling is per tenant. Back off on 429/503 and honour `Retry-After`.
