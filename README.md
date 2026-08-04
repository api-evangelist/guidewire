# Guidewire (guidewire)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Guidewire provides the insurance industry's leading platform including PolicyCenter, ClaimCenter, and BillingCenter. REST APIs enable policy lifecycle management, claims processing, payment orchestration, and underwriting workflows for P&C insurance carriers on the Guidewire Cloud platform.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/guidewire/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/guidewire/refs/heads/main/apis.yml)

## Tags

- Insurance
- Policy
- Claims
- Billing
- P&C

## Timestamps

- **Modified:** 2026-04-28

## APIs

### Guidewire PolicyCenter API

The Guidewire PolicyCenter API provides REST endpoints for policy lifecycle management, underwriting workflows, policy issuance, endorsements, renewals, and cancellations for property and casualty insurance carriers.

- **Human URL:** [https://docs.guidewire.com/](https://docs.guidewire.com/)
- **Base URL:** `https://api.guidewire.com`

#### Tags

- Insurance
- P&C
- Policy
- Underwriting

#### Properties

- [Documentation](https://docs.guidewire.com/)
- [Getting Started](https://www.guidewire.com/developers)
- [OpenAPI](openapi/guidewire-policycenter-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/guidewire-policycenter.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/guidewire-policycenter.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Guidewire ClaimCenter API

The Guidewire ClaimCenter API provides REST endpoints for claims intake, assignment, investigation, reserving, payment processing, and closure workflows for P&C insurance claim operations.

- **Human URL:** [https://docs.guidewire.com/](https://docs.guidewire.com/)
- **Base URL:** `https://api.guidewire.com`

#### Tags

- Claims
- Insurance
- Loss Adjustment
- P&C

#### Properties

- [Documentation](https://docs.guidewire.com/)
- [Getting Started](https://www.guidewire.com/developers)
- [OpenAPI](openapi/guidewire-claimcenter-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/guidewire-claimcenter.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/guidewire-claimcenter.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Guidewire BillingCenter API

The Guidewire BillingCenter API provides REST endpoints for payment orchestration, invoice generation, payment plans, disbursements, and collections management for insurance billing operations.

- **Human URL:** [https://docs.guidewire.com/](https://docs.guidewire.com/)
- **Base URL:** `https://api.guidewire.com`

#### Tags

- Billing
- Insurance
- P&C
- Payments

#### Properties

- [Documentation](https://docs.guidewire.com/)
- [Getting Started](https://www.guidewire.com/developers)
- [Postman Collection](collections/guidewire-claimcenter.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/guidewire-claimcenter.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/guidewire-policycenter.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/guidewire-policycenter.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Guidewire Integration Gateway API

The Guidewire Integration Gateway provides a managed API layer for connecting Guidewire Cloud applications to third-party systems, enabling event-driven integrations and REST API extensions for the Guidewire insurance platform.

- **Human URL:** [https://docs.guidewire.com/](https://docs.guidewire.com/)
- **Base URL:** `https://api.guidewire.com`

#### Tags

- Insurance
- Integration
- Middleware
- P&C

#### Properties

- [Documentation](https://docs.guidewire.com/)
- [Getting Started](https://www.guidewire.com/developers)
- [AsyncAPI](asyncapi/guidewire-integration-gateway-asyncapi.yml) — [AsyncAPI Specification](https://www.asyncapi.com/docs/reference/specification/latest)
- [Postman Collection](collections/guidewire-claimcenter.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/guidewire-claimcenter.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/guidewire-policycenter.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/guidewire-policycenter.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [LinkedIn](https://www.linkedin.com/company/guidewire-software)
- [L L Ms Txt](https://docs.guidewire.com/llms.txt)
