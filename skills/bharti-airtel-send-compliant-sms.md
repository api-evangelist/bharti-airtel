---
name: Send a DLT-compliant SMS with Airtel IQ
description: >-
  Send single, bulk or CSV-batch A2P SMS through Airtel IQ over Airtel's pan-India network,
  satisfying TRAI DLT registration requirements and handling Airtel's in-band error reporting.
api: openapi/bharti-airtel-iq-sms-openapi.yml
base_url: https://iqsms.airtel.in/api/v1
operations:
  - sendSmsUsingPOST_2
  - sendSmsUsingGET
  - sendSmsUsingPOST
  - sendSmsViaCsvUsingPOST
  - sendSmsViaCsvUsingPOST_1
generated: '2026-07-25'
method: generated
source: openapi/bharti-airtel-iq-sms-openapi.yml + https://www.airtel.in/business/b2b/airtel-iq/api-docs/sms/overview
---

# Send a DLT-compliant SMS with Airtel IQ

## Before you start

You need three things that Airtel does not issue:

1. **DLT registration.** India's TRAI requires every A2P sender to register on the DLT
   (distributed ledger) platform. Registration gives you an `entityId` (your principal-entity id)
   and a `dltTemplateId` per approved message template. Airtel scrubs against these in-path — a
   send without them is rejected, not queued.
2. **A registered sender header** (`sourceAddress`) — the shortcode or sender id your message goes
   out under.
3. **Airtel IQ credentials.** Get them from the IQ dashboard
   (https://www.airtel.in/business/b2b/airtel-ccp/dashboard/). A trial account is self-serve and
   ships 1 Intelliphone and 500 free pulses.

## Authenticate

Every request uses HTTP Basic over HTTPS:

```
Authorization: Basic base64(username:password)
Content-Type: application/json
```

There is no OAuth, no token exchange and no scope on this API. HTTPS is mandatory.

## Send one message

Use `sendSmsUsingPOST_2` — `POST /api/v1/send-sms` with a `SingleSmsRequestVO` body.

Required fields: `customerId`, `dltTemplateId`, `entityId`, `message`, `messageType`,
`sourceAddress`. Set `destinationAddress` to the recipient MSISDN.

`messageType` must be one of `PROMOTIONAL`, `TRANSACTIONAL`, `SERVICE_IMPLICIT`,
`SERVICE_EXPLICIT` — pick the class your DLT template was approved under, not the one that sounds
closest. On the GET variant (`sendSmsUsingGET`) `INTERNATIONAL` is also accepted.

Optional: `filterBlacklistNumbers` (drop numbers on the blacklist before sending), `priority`,
`metaData` (free-form object echoed back on the response), and `urlShortenerParams` if you want
Airtel to shorten links and attribute clicks to a campaign.

Prefer the POST form. `sendSmsUsingGET` accepts the same fields as query parameters and will put
the message body and recipient number in the URL — and therefore in logs and proxies.

## Send many messages

- `sendSmsUsingPOST` — `POST /api/v1/send-sms-bulk`, body is an **array** of `SingleSmsRequestVO`.
  Multiple distinct messages in one call.
- `sendSmsViaCsvUsingPOST_1` — `POST /api/v1/send-sms-csv`, `multipart/form-data` with a `file`
  field. One message to many recipients from a CSV.
- `sendSmsViaCsvUsingPOST` — `POST /api/v1/send-sms-cm`, the content-moderation route. This is the
  documented way to send **without providing DLT details**; `customerId` moves to the query string
  and the body is a `CMRequest`. Use it only where your account is provisioned for it.

## Read the response — errors arrive on a 200

This API has no error status codes. The specification declares only `200` for all five operations,
and failures come back inside `SendSmsResponseVO`:

- `errorMessage` — the error text for the request. **Check it on every call.** A 200 with a
  populated `errorMessage` is a failure.
- `incorrectNum[]` — recipient numbers rejected as malformed (fewer than 10 digits). Partial
  success is normal on bulk and CSV sends: some numbers go, some land here.
- `messageRequestId` — the unique id for the submitted request. Keep it; it is your only handle
  for downstream reporting.

## Retries — be careful

Airtel IQ has **no idempotency key**. There is no `Idempotency-Key` header, no request-id you can
supply, and no de-duplication window. `messageRequestId` is assigned by Airtel *after* submission,
so it cannot make a retry safe.

A retried send delivers a second real SMS to a real handset and bills a second time. On a timeout
or an ambiguous response, do not retry blind — reconcile first through the reporting surface, or
accept the duplicate deliberately.

## Rate limits

No `X-RateLimit-*` or RFC 9331 headers are published on this API. You cannot see your remaining
budget without exhausting it. Pace bulk sends conservatively.

## Related artifacts

- `authentication/bharti-airtel-authentication.yml`
- `conventions/bharti-airtel-conventions.yml`
- `errors/bharti-airtel-problem-types.yml`
- `sandbox/bharti-airtel-sandbox.yml`
