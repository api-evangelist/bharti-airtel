---
name: Manage the Airtel IoT SIM lifecycle
description: >-
  Authenticate against Airtel IoT, inventory SIMs, drive the full SIM lifecycle (test activation,
  live activation, plan change, swap, safe custody, temporary disconnection and resume) as
  asynchronous jobs, and track those jobs to completion.
api: openapi/bharti-airtel-iot-openapi.yml
base_url: https://m2m.airteliot.co.in/iot/api/
operations:
  - generateAccessTokenUsingPOST
  - refreshAccessTokenUsingPOST
  - fetchCustomerBasketsUsingGET
  - fetchCustomerSimsUsingGET
  - fetchCustomerPlansUsingGET_1
  - activateTestSimsUsingPOST
  - activateSimFromTestUsingPOST
  - activateSimsUsingPOST
  - changePlanUsingPOST
  - swapSimUsingPOST
  - safeCustodyUsingPOST
  - outOfSafeCustodyUsingPOST
  - tempDisconnectionUsingPOST
  - tdResumeUsingPOST
  - fetchJobsUsingGET
  - fetchOrdersUsingGET
  - fetchDeviceInfoUsingGET
  - fetchDataUsageUsingGET
  - fetchSimKycDetailsListUsingGET
  - updateSimKycDetailsUsingPOST
generated: '2026-07-25'
method: generated
source: openapi/bharti-airtel-iot-openapi.yml (Airtel IoT, OpenAPI 3.0.1, 43 operations)
---

# Manage the Airtel IoT SIM lifecycle

Airtel IoT is a connectivity-management API over Airtel's M2M SIM estate: 43 operations across
account authorization, account details, inventory, SIM lifecycle, bulk lifecycle, job status,
device and session information, whitelisting, KYC and a messaging centre. Documentation is public;
credentials are enterprise-issued.

The central pattern: **lifecycle changes are jobs, not synchronous mutations.** You submit a
change, you get a job, you track the job.

## 1. Authenticate

`generateAccessTokenUsingPOST` — `POST /auth/v2/generate/authtoken`

Send `client_id` and `client_secret` as `application/x-www-form-urlencoded`. Refresh with
`refreshAccessTokenUsingPOST` — `POST /auth/v2/refresh/authtoken`, passing the `refresh_token`.

Note that no `securityScheme` is declared in the specification and the document sets
`security: []` at the root — the token is real, but the spec does not tell you where to put it.
Work from the documentation page, not from the spec, when wiring auth.

There are **no scopes**. One undifferentiated token reaches all 43 operations, including SIM swap
and disconnection. Treat the credential accordingly: it is a full-control credential over the SIM
estate.

## 2. Find your SIMs

- `fetchCustomerBasketsUsingGET` — your inventory baskets.
- `fetchCustomerSimsUsingGET` — `GET /details/basket/{basketId}/sims`, the SIMs in a basket.
- `fetchCustomerPlansUsingGET_1` — `GET /details/plans`, the plans available to you. You need a
  plan identifier before an activation or a plan change.

## 3. Drive the lifecycle

Every one of these is a `POST` under `/job/` and returns a job:

| Intent | Operation | Path |
|---|---|---|
| Activate in test mode | `activateTestSimsUsingPOST` | `POST /job/activate/test/sims` |
| Promote test → live | `activateSimFromTestUsingPOST` | `POST /job/activate/sim/from/test` |
| Activate live | `activateSimsUsingPOST` | `POST /job/sim/activate` |
| Change plan | `changePlanUsingPOST` | `POST /job/change/plan` |
| Swap SIM | `swapSimUsingPOST` | `POST /job/sim/swap` |
| Safe custody (voluntary suspend) | `safeCustodyUsingPOST` | `POST /job/sim/voluntary/suspend` |
| Out of safe custody | `outOfSafeCustodyUsingPOST` | `POST /job/sim/voluntary/resume` |
| Temporary disconnection | `tempDisconnectionUsingPOST` | `POST /job/temp/disconnect` |
| Resume from TD | `tdResumeUsingPOST` | `POST /job/td/resume` |

Test mode is the closest thing Airtel offers to a sandbox: `activateTestSimsUsingPOST` puts a SIM
into a test state on the **production** platform, and `activateSimFromTestUsingPOST` promotes it.
There is no separate sandbox host and no test credential prefix.

For estate-wide changes use the bulk surface — `downloadBulkOrderTemplateUsingPOST` to get the
template, `bulkUploadUsingPOST` to submit it, `fetchDownloadHistoryUsingGET` and
`fetchJobHistoryUsingGET` to audit.

## 4. Track the job

`fetchJobsUsingGET` — `GET /job/all` lists jobs. `fetchOrdersUsingGET` — `GET /job/{jobId}/order`
returns the order ids inside a job. A lifecycle call that returned 200 or 201 has been *accepted*,
not *completed*; poll until the job resolves before asserting the SIM is in its new state.

## 5. Observe

- `fetchDeviceInfoUsingGET` — `GET /sim/device/info`
- `fetchDataUsageUsingGET` — `GET /sim/data/usage`
- `deviceDiagnoseRadiusUsingGET` — RADIUS-level session diagnostics
- `fetchSimKycDetailsListUsingGET` / `updateSimKycDetailsUsingPOST` — Indian KYC obligations are
  first-class here; a SIM's KYC record is part of the API, not a back-office concern.

## Safety rules for agents

These operations are **consequential and irreversible in the field**. `swapSimUsingPOST`,
`tempDisconnectionUsingPOST` and `safeCustodyUsingPOST` take a deployed device off the network. A
SIM in a vehicle, a meter or a medical device stops communicating.

And Airtel publishes **no idempotency contract** — no `Idempotency-Key` header, no de-duplication
window, nothing. A retried lifecycle POST submits a second job. On a timeout, poll
`fetchJobsUsingGET` to find out what actually happened before resubmitting. Never blind-retry.

The error surface is thin: the specification declares a generic `400` and `500` on all 43
operations with no problem-details schema, so you cannot distinguish "invalid ICCID" from
"plan not permitted" from the status code alone. Read the response body.

See `agentic-access/bharti-airtel-agentic-access.yml` for the per-operation execution contract —
the SIM lifecycle writes are the operations classified at the highest consequence level in this
provider's estate.

## Related artifacts

- `authentication/bharti-airtel-authentication.yml`
- `conventions/bharti-airtel-conventions.yml`
- `errors/bharti-airtel-problem-types.yml`
- `sandbox/bharti-airtel-sandbox.yml`
- `data-model/bharti-airtel-data-model.yml`
