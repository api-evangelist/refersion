# Refersion (refersion)

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

Refersion is an affiliate marketing and influencer tracking platform for e-commerce brands. It provides REST and GraphQL APIs for managing affiliates, tracking referrals and conversions, processing commissions, and integrating with Shopify, BigCommerce, WooCommerce, Magento, and other e-commerce platforms. The platform offers first-party tracking technology designed to work within modern privacy constraints from Apple and Google.

APIs.json: https://raw.githubusercontent.com/api-evangelist/refersion/refs/heads/main/apis.yml

Naftiko: https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=refersion-api-evangelist&utm_content=repo

## Tags

- Affiliate Marketing
- Influencer Marketing
- E-Commerce
- Referral Tracking
- Commission Management
- Shopify

## APIs

- **Refersion REST API** — RESTful API for managing affiliates, tracking conversions, processing commissions, configuring SKU-level commission rates, and generating performance reports. Documentation: https://www.refersion.dev/reference/welcome-to-refersion
- **Refersion GraphQL API** — GraphQL API with an explorer interface for querying affiliate data and managing program configurations. Documentation: https://www.refersion.dev/reference/graphql-overview
- **Refersion Webhooks API** — Real-time webhook notifications for affiliate activity including conversions, payments, affiliate status changes, and bonus tier movements. Documentation: https://www.refersion.dev/reference/webhooks-for-merchants

## Plans, Rate Limits, and FinOps

- **Plans**: [plans/refersion-plans-pricing.yml](plans/refersion-plans-pricing.yml) — Three tiers: Launch ($39/month), Growth ($299/month), and Scale (custom/enterprise). Full developer API and webhook access requires the Scale plan.
- **Rate Limits**: [rate-limits/refersion-rate-limits.yml](rate-limits/refersion-rate-limits.yml) — API key-based rate limiting; specific thresholds not publicly documented. Webhook access restricted to Scale plan and above.
- **FinOps**: [finops/refersion-finops.yml](finops/refersion-finops.yml) — Cost allocation and optimization guidance including annual billing savings and transaction fee avoidance strategies.

## Timestamps

- Created: 2026-06-13
- Modified: 2026-06-13

## Common

- Website: https://www.refersion.com/
- Documentation: https://www.refersion.dev/reference/welcome-to-refersion
- GitHub Org: https://github.com/refersion
- LinkedIn: https://www.linkedin.com/company/refersion
- Blog: https://www.refersion.com/blog/
- Pricing: https://www.refersion.com/pricing/
- Status Page: https://statuspage.refersion.com
- X: https://x.com/refersion

## Maintainers

- Kin Lane (kin@apievangelist.com)
