---
name: orange-business-sim-swap-takeover-check
description: Decide whether a high-risk action (password reset, payout, SMS OTP) should proceed, by asking Orange's network whether the customer's SIM was recently swapped and whether the handset is reachable.
api: Orange Business CAMARA Network APIs
generated: '2026-08-26'
method: generated
source: openapi/_original/orange-business-sim-swap-openapi.yml, openapi/_original/orange-business-device-reachability-status-openapi.yml, openapi/_original/orange-business-device-swap-openapi.yml
operations:
  - checkSimSwap
  - retrieveSimSwapDate
  - getReachabilityStatus
  - checkDeviceSwap
  - retrieveDeviceSwapDate
---

# Check for SIM-swap account takeover before a high-risk action

Use this before sending an SMS OTP, accepting a password reset, or releasing a payout. A SIM swap in
the last few hours is the signal that the phone number no longer belongs to the person you think it
does.

## Before you start

- Base URL: `https://api.orange.com/camara/playground/api/sim-swap/v1` for the mock playground.
  Production swaps the base URL and the credentials; the request and response shapes do not change.
- Token: `POST https://api.orange.com/oauth/v3/token` with `grant_type=client_credentials` and
  `Authorization: Basic base64(client_id:client_secret)`. Cache the token — it carries `expires_in`
  (3600s) and Orange asks that you not call the token endpoint per request.
- Phone numbers are E.164 (`+33712345678`).
- Rate limit: 25 requests/second across all Orange APIs. Exhaustion returns **HTTP 403 with
  `code: 53`**, not 429 — treat a 403 carrying code 53 as back-off, not as an authorization failure.
  There is no `Retry-After` header; wait at least one second.

## Steps

1. **Ask whether a swap happened inside your risk window.**
   Call `checkSimSwap` (`POST /check`) with `phoneNumber` and `maxAge` in hours (e.g. `240`).
   It returns `{ "swapped": true|false }`.

2. **If `swapped` is true, get the timestamp.**
   Call `retrieveSimSwapDate` (`POST /retrieve-date`) with the same `phoneNumber`. Use the returned
   date to decide how hard to fail — a swap 3 hours ago is a different risk from one 9 days ago.

3. **Check the handset changed too.**
   Call `checkDeviceSwap` and, if needed, `retrieveDeviceSwapDate` from the Device Swap API. A SIM
   swap *and* a device swap inside the same window is the strongest takeover signal Orange exposes.

4. **Confirm the line is actually reachable.**
   Call `getReachabilityStatus` (`POST /retrieve`) on the Device Reachability Status API. A response
   of `NOT_CONNECTED`, or `CONNECTED_SMS` where you expected data, tells you an OTP may never land.

5. **Decide.** Fail closed on a recent swap. Do not fail closed on a network error — see below.

## Errors you must handle

The CAMARA envelope is `{ "status": <int>, "code": "<STRING>", "message": "..." }`.

| code | status | what it means for this flow |
|---|---|---|
| `IDENTIFIER_NOT_FOUND` | 404 | The number is not an Orange customer. Not a fraud signal — fall back to your other checks. |
| `SERVICE_NOT_APPLICABLE` | 422 | Orange cannot answer for this number. Not a fraud signal. |
| `UNNECESSARY_IDENTIFIER` | 422 | You sent `phoneNumber` while using a 3-legged token that already identifies the device. Drop the field. |
| `MISSING_IDENTIFIER` | 422 | No number provided and the token does not identify one. |
| `INVALID_TOKEN_CONTEXT` | 403 | `phoneNumber` is not consistent with the access token. |
| `UNAUTHENTICATED` | 401 | Token missing, invalid or expired. Refresh once, then stop. |
| `TOO_MANY_REQUESTS` / `QUOTA_EXCEEDED` | 429 | Back off. |

**Never treat an error as a clean result.** `IDENTIFIER_NOT_FOUND` and `SERVICE_NOT_APPLICABLE` mean
*unknown*, not *safe*.

## Rehearsing it

Use the published playground numbers — every one has a documented outcome:

- `+990100000000` — clean baseline, `swapped: false`
- `+990100000001` — SIM issued within 7 days, `swapped: true`
- `+990100000005` — device changed within 6 hours
- `+990100000006` — SIM *and* device both recently swapped
- `+990100000013` — every fraud signal active at once
- `+990100000003` — `NOT_CONNECTED`, so no OTP will arrive

Full list in `sandbox/orange-business-sandbox.yml`.

## Things to know before you act

- **No idempotency key.** Orange documents none anywhere. These calls are reads, so a retry is safe
  here — but do not carry that assumption into the write APIs.
- **These calls are not reversible or refundable** — they are billed queries against a live network.
  Production pricing is not published; access runs through Orange sales.
- **Consent is carried in the OAuth scope**, as `openid dpv:<dpvValue>`, using W3C Data Privacy
  Vocabulary purpose values. You must know the purpose before you can request the token.
