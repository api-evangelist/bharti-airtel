---
name: Locate a device with subscriber consent using Airtel Locate
description: >-
  Register an MSISDN, capture explicit subscriber consent over SMS or IVR, then fetch the device's
  network location synchronously or via an asynchronous callback — with the consent lifecycle,
  expected network-failure rate and rate ceilings handled correctly.
api: openapi/bharti-airtel-locate-openapi.yml
base_url: https://openapi.airtel.in/locate/apis
operations:
  - getOrCreateOauthTokenUsingPOST_2
  - initiateConsentUsingPOST_3
  - getConsentUsingGET_2
  - searchUsingGET_2
  - findAllUsingGET_2
  - getLocationUsingGET_3
  - patchResourceUsingPATCH_3
  - deleteResourceUsingDELETE_3
  - addressValidationUsingPOST_1
generated: '2026-07-25'
method: generated
source: openapi/bharti-airtel-locate-openapi.yml (Locate API Catalog 1.4.8)
---

# Locate a device with subscriber consent using Airtel Locate

Airtel Locate returns a device's position from the **network** — no GPS, no SDK on the handset. A
Locate *Resource* is an MSISDN: a valid 10 or 13 digit Airtel mobile number. Every path is scoped
by your `customerBaId` (Airtel billing-account id).

The important thing to internalise: **a valid access token is not enough**. The subscriber must
have consented, and consent is a state machine you have to drive.

## 1. Get a token

`getOrCreateOauthTokenUsingPOST_2` — `POST /locate/apis/customers/{customerBaId}/oauth2_token`

JSON body: `client_id`, `client_secret`, `scope`. Scope is an enum with exactly two values:

- `location` — required to call the Location API. The specification is explicit: "The access token
  must be generated with 'location' scope for using this API."
- `resource` — the consent and resource-management surface.

The response is `OauthAccessToken` (`access_token`, `token_type`, `expires_in`); the token is valid
for 24 hours.

Send it on every subsequent call in the **`access_token` request header**. This is not
`Authorization: Bearer` — Locate models it as a plain required header parameter, and a client that
assumes RFC 6750 will get 401s.

## 2. Initiate consent

`initiateConsentUsingPOST_3` — `POST /locate/apis/customers/{customerBaId}/resources/consent`

Body is a `ConsentRequest`: `msisdn` (required, prefixed `91` then the 10 digits), `channel`
(`SMS`, default, or IVR), `language` (default `EN`; also `HI`, `AS`, `BN`, `OR`, `KN`, `ML`, `TA`,
`TE`).

Returns **202 Accepted** — consent is asynchronous by nature. Use this same operation to re-initiate
after a `REJECTED` state or to re-ask in a different language.

Failure modes worth handling: `400` for an invalid language, an invalid channel, or a number that
is not an Airtel number; `403` for a forbidden consent state or channel.

## 3. Wait for `ALLOWED`

`getConsentUsingGET_2` — `GET /locate/apis/customers/{customerBaId}/resources/{msisdn}/consent`

Consent states: `PROCESSING` → `INITIATED` → `PENDING` → `ALLOWED` | `REJECTED` | `FAILED`.
Consent events: `MT` (consent SMS sent), `DR` (delivery report for the MT), `MO` (valid consent
received from the user), `CALL` (IVR consent call placed), `CDR` (call record for that call).

Poll here, or list your whole estate with `findAllUsingGET_2`
(`GET /.../resources`) filtered by `consent`, `tracking` and `daysSinceLastLocationFetched`, paged
with `page` (from 0), `size` (default 20) and `sort`. `searchUsingGET_2` returns one MSISDN's
name, consent status and tracking status.

**Only `ALLOWED` unlocks location.** Anything else returns 403 with "resource has not given
consent".

## 4. Fetch the location

`getLocationUsingGET_3` — `GET /locate/apis/customers/{customerBaId}/resources/{msisdn}/location`

Required query parameter `type`: `sync` (default) or `async`.

- **sync** → `200` with a `LocationResponse`: `location.latitude` / `location.longitude`, `status`,
  `message`, `retrievedAt`.
- **async** → `202` with an `AsyncCorrelationId` (`correlationId`). Airtel then POSTs the location
  to **your** registered callback listener. You must register that listener with Airtel out of band
  first — there is no registration API, and if you skip it the request fails with 403 "callback url
  is not registered". Match the callback to the request on `correlationId`.

### Errors you must plan for

- `403` — subscription expired, subscription limit reached, no consent, tracking disabled, or no
  callback URL registered. Airtel enforces commercial ceilings as 403, not 429.
- `429` — the licence's location rate has been hit, **or** a location request is already in flight
  for that MSISDN. Do not fan out concurrent requests for the same number.
- `502` — HLR / MME / MSC network error. The specification says plainly: "The ~5-10% of such error
  is expected." Treat a 502 as normal operating noise with backoff, not as an incident.

## 5. Manage the resource

- `patchResourceUsingPATCH_3` — change the alias (name) or turn `tracking` on/off. A `403` here
  means the licence's issued-number allowance is exhausted.
- `deleteResourceUsingDELETE_3` — remove an MSISDN (`204` on success). This is the withdrawal path;
  use it when a subscriber revokes.

## Adjacent capability

`addressValidationUsingPOST_1` — `POST /.../resources/{msisdn}/validation/address` returns the
distance between the live network location and a supplied physical address. Access is separately
granted; email locate.support@airtel.com.

## Tenant variants

Every operation above has a Tenant-scoped twin (`findAllUsingGET_3`, `initiateConsentUsingPOST_4`,
`getConsentUsingGET_3`, `getLocationForTenantsUsingGET_1`, `addressValidationForTenantsUsingPOST_1`,
`deleteResourceUsingDELETE_4`, `patchResourceUsingPATCH_4`, `searchUsingGET_3`) for resellers
managing consent on behalf of their own customers. Same semantics, tenant-scoped paths.

## What this API is not

Airtel Locate is Airtel's proprietary product. It is **not** the CAMARA DeviceLocation or
LocationVerification API, and Airtel publishes no CAMARA surface. Do not treat it as portable
across operators.

## Related artifacts

- `authentication/bharti-airtel-authentication.yml`
- `scopes/bharti-airtel-scopes.yml`
- `asyncapi/bharti-airtel-webhooks.yml`
- `conventions/bharti-airtel-conventions.yml`
