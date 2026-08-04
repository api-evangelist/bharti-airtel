# Bharti Airtel (bharti-airtel)

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

Bharti Airtel Limited is an Indian multinational telecommunications operator headquartered in New Delhi, and one of the two dominant mobile network operators in its home market of India. It runs mobile (2G/4G/5G), fixed broadband, DTH, enterprise connectivity, data centre and payments-bank businesses across India and South Asia, and through the separately listed Airtel Africa across fourteen African countries. In the value chain Airtel is an access-network owner: it holds the spectrum, the SIM estate, the subscriber identity and the network signalling that the rest of the industry resells.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/bharti-airtel/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/bharti-airtel/refs/heads/main/apis.yml)

## The split

Airtel's API posture divides cleanly in two, and the division is the whole story.

**The connectivity products are genuinely API-native.** Airtel IQ (CPaaS) publishes open Swagger for SMS and CDR reporting and offers a self-serve trial account. The Airtel IoT and Airtel Locate developer portals publish complete OpenAPI 3.0 catalogues — 43 and 19 operations — as Redoc pages that need no login at all.

**The network APIs are not reachable from Airtel.** Airtel is a GSMA Open Gateway signatory, a founding operator shareholder in Aduna, and has commercially launched a CAMARA SIM Swap API to Indian banks. Yet it publishes no CAMARA endpoint, no CAMARA specification, no CIBA authorization surface and no network-API developer portal of its own. Developers reach Airtel's network capabilities only through aggregators — Aduna, and Nokia's Network as Code platform — never directly.

## Tags

- Telecommunications
- India
- Mobile Network Operator
- Network APIs
- CAMARA
- Open Gateway
- SIM Swap
- CPaaS
- Messaging
- SMS
- RCS
- Voice
- IoT
- M2M
- Device Location
- Broadband
- 5G
- Identity Verification
- Carrier Billing
- Consent Management

## Timestamps

- **Created:** 2026-07-25
- **Modified:** 2026-07-25

## APIs

### Airtel IQ SMS API

The messaging half of Airtel IQ, Airtel's network-embedded CPaaS suite. Sends single, bulk, CSV-batch and content-moderated A2P SMS over Airtel's pan-India network, with TRAI DLT (Hyperledger-based distributed ledger) scrubbing for sender header, content template and consent compliance applied in-path.

- **Human URL:** [https://www.airtel.in/business/b2b/airtel-iq/api-docs/sms/sms-utility](https://www.airtel.in/business/b2b/airtel-iq/api-docs/sms/sms-utility)
- **Base URL:** `https://iqsms.airtel.in/api/v1`
- **Auth:** HTTP Basic, API-key credentials from the IQ dashboard

#### Properties

- [Documentation](https://www.airtel.in/business/b2b/airtel-iq/api-docs/sms/overview)
- [API Reference](https://www.airtel.in/business/b2b/airtel-iq/api-docs/sms/sms-utility)
- [OpenAPI](openapi/bharti-airtel-iq-sms-openapi.yml) — Swagger 2.0
- [Sign Up](https://www.airtel.in/business/b2b/airtel-ccp/dashboard/#/signup)

### Airtel IQ Reporting API

The Call Detail Record (CDR) reporting surface of Airtel IQ. Returns per-call time, date, duration, caller ID, destination number, status and call-recording URL for voice traffic placed through Airtel IQ Intelliphones and call flows.

- **Human URL:** [https://www.airtel.in/business/b2b/airtel-iq/api-docs/voice/sample-cdr](https://www.airtel.in/business/b2b/airtel-iq/api-docs/voice/sample-cdr)
- **Base URL:** `https://openapi.airtel.in/gateway/airtel-xchange-reporting/v2`

#### Properties

- [Documentation](https://www.airtel.in/business/b2b/airtel-iq/api-docs/voice/overview)
- [API Reference](https://www.airtel.in/business/b2b/airtel-iq/api-docs/voice/sample-cdr)
- [OpenAPI](openapi/bharti-airtel-iq-reporting-openapi.yml) — Swagger 2.0

### Airtel IoT API

Airtel's IoT/M2M connectivity-management API — 43 operations across account authorization, account details, inventory management, SIM lifecycle (activate, swap, change plan, safe custody, temporary disconnection), bulk SIM lifecycle, job status, device and session information, SMS and voice whitelisting, KYC management, and a messaging centre.

- **Human URL:** [https://www.airtel.in/m2m/business/b2b/m2m-solutions/documentation/m2m-solutions/](https://www.airtel.in/m2m/business/b2b/m2m-solutions/documentation/m2m-solutions/)
- **Base URL:** `https://m2m.airteliot.co.in/iot/api/`
- **Auth:** OAuth 2.0 client-credentials with refresh tokens

#### Properties

- [Documentation](https://www.airtel.in/m2m/business/b2b/m2m-solutions/documentation/m2m-solutions/)
- [OpenAPI](openapi/bharti-airtel-iot-openapi.yml) — OpenAPI 3.0.1, 42 paths, 63 schemas

### Airtel Locate API

Airtel's network-based device-location platform, letting enterprises track a device by MSISDN without relying on the device's GPS. Nineteen operations across Authorization, Resource Consent, Location, Address Validation and Tenant management, with explicit per-subscriber consent capture and an asynchronous mode that posts location to a customer-registered callback listener.

This is Airtel's proprietary location API — **not** the CAMARA Device Location or Location Verification API.

- **Human URL:** [https://www.airtel.in/m2m/business/b2b/locate-solution/documentation/locate-solution/](https://www.airtel.in/m2m/business/b2b/locate-solution/documentation/locate-solution/)
- **Base URL:** `https://openapi.airtel.in/locate/apis`
- **Auth:** OAuth 2.0 bearer token plus consent gate

#### Properties

- [Documentation](https://www.airtel.in/m2m/business/b2b/locate-solution/documentation/locate-solution/)
- [OpenAPI](openapi/bharti-airtel-locate-openapi.yml) — OpenAPI 3.0.0, v1.4.8

### Airtel Smart API (legacy)

Airtel's original partner developer programme, launched in 2017 and now unmaintained — the site still carries a 2017 copyright. Its public documentation still describes real OAuth 2.0 authorization-code and implicit flows and three partner services: carrier-billing charge, subscription activation/deactivation, and an LRN (local routing number) and porting lookup, plus an Aadhaar/UIDAI DigiAuth authentication and e-KYC integration. Production hosts are redacted to `<IP>:<Port>`, so no specification was harvestable.

- **Human URL:** [https://openapi.airtel.in/smartapi/#/docs](https://openapi.airtel.in/smartapi/#/docs)

## CAMARA and GSMA Open Gateway posture

| Signal | Finding |
| --- | --- |
| GSMA Open Gateway member | **Yes** — one of the 21 founding carriers (MWC, February 2023) |
| Aduna | **Yes** — founding operator shareholder in the Ericsson-led JV (July 2025) |
| CAMARA APIs launched | **SIM Swap** — commercially live to Indian banks via a GSMA-certified federated Airtel/Jio/Vi channel (GSMA, 9 October 2025) |
| CAMARA APIs announced only | **Number Verification** — "planned before the end of 2025"; no endpoint or spec found |
| CAMARA endpoint on an Airtel host | **None** |
| CAMARA OpenAPI published by Airtel | **None** |
| CIBA | **Absent** from every public Airtel surface |
| Listed on github.com/camaraproject | **No** |
| Channel to developers | Aduna, and Nokia Network as Code — never Airtel directly |

A commercially launched SIM Swap API is stronger than a press release. It is still materially weaker than a developer surface: there is no way for a developer to discover, read about, or call a CAMARA API from anything Airtel operates.

## TM Forum

No TM Forum Open API conformance certification was found, and no TMF-numbered API is published on any Airtel host. The legacy Smart API portal's router still whitelists a public `/docs/Tmforum` route, but the corresponding content has been removed — a dangling route, not an implementation.

## Dead and missing developer hosts

`developer.airtel.in`, `docs.airtel.in`, `api.airtel.in`, `opengateway.airtel.in`, `developers.opengateway.airtel.in` and `iot.airtel.in` all fail to resolve or connect. `airtel.in/developer`, `/developers` and `/opengateway` return HTTP 200 but serve the generic consumer homepage — soft 404s, not portals.

## Notes

Full reviewer findings, HTTP statuses and spec provenance are in [review.yml](review.yml). Verbatim source specifications are preserved under [openapi/_original/](openapi/_original/).
