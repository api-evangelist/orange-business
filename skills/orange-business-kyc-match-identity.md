---
name: orange-business-kyc-match-identity
description: Check the identity details a customer typed against what their mobile operator already holds, and confirm the handset in their hand owns the number they claim.
api: Orange Business CAMARA Network APIs
generated: '2026-08-26'
method: generated
source: openapi/_original/orange-business-kyc-match-openapi.yml, openapi/_original/orange-business-number-verification-openapi.yml
operations:
  - KYC_Match
  - phoneNumberVerify
  - phoneNumberShare
---

# Match a customer against operator records

Two different questions, often confused:

- **`phoneNumberVerify` / `phoneNumberShare`** (Number Verification) — *is this handset the owner of
  this number?* Answered by the mobile network authenticating the device, not by an SMS code.
- **`KYC_Match`** — *do the name, address and birth date this person typed match what their operator
  holds?* Returns per-field match results, never the operator's stored values.

## Steps

1. **Prove the handset owns the number.**
   - `phoneNumberVerify` (`POST /verify`) — you supply the number; Orange confirms or denies it is
     the device's own number.
   - `phoneNumberShare` (`GET /device-phone-number`) — Orange returns the device's number. Use only
     when you genuinely need the number, not merely to check one.

   This requires the device to be on the mobile network. If it is on Wi-Fi you get
   `NUMBER_VERIFICATION.USER_NOT_AUTHENTICATED_BY_MOBILE_NETWORK` (403) — a *setup* problem, not a
   fraud signal. Fall back to a different check rather than refusing the customer.

2. **Match the identity fields.**
   `KYC_Match` (`POST /match`) with any combination of `name`, `givenName`, `familyName`,
   `address`, `birthdate`, `email` and the rest. You get a per-field match indicator back.

   `KNOW_YOUR_CUSTOMER.INVALID_PARAM_COMBINATION` (400) means the combination you sent is not one
   Orange accepts — re-read the field list for the country deployment you are calling, because
   Orange runs KYC Match separately per country (`fr/0.2`, `be/0.2`, `es/0.2`, `ro/0.2`) and the
   supported fields differ.

3. **Score, do not gate on a single field.** A mismatched address line is weak evidence; a
   mismatched birth date is strong. Orange gives you the fields; the policy is yours.

## Errors

| code | status | meaning |
|---|---|---|
| `IDENTIFIER_NOT_FOUND` | 404 | "The phone number provided is not associated with a customer account". Not fraud — an unknown. |
| `SERVICE_NOT_APPLICABLE` | 422 | Not available for this number. |
| `KNOW_YOUR_CUSTOMER.INVALID_PARAM_COMBINATION` | 400 | Field combination not supported. |
| `NUMBER_VERIFICATION.USER_NOT_AUTHENTICATED_BY_MOBILE_NETWORK` | 403 | Device not on the mobile network. |
| `INVALID_TOKEN_CONTEXT` | 403 | `phoneNumber` inconsistent with the access token. |
| `UNNECESSARY_IDENTIFIER` | 422 | 3-legged token already identifies the device; drop the identifier. |

## Rehearsing it

- `+990100000008` — every KYC field populated, PAYM since 2015, Toulouse. The happy path.
- `+990100000000` — nominal long-standing postpaid customer.
- `+990100000011` — subscribed less than 20 days ago. Pair this with KYC Tenure when a young
  account is itself the risk.
- `+990100000013` — every fraud signal active at once.

## Before you act

- This is personal data under a consent regime. The purpose travels in the OAuth scope as
  `openid dpv:<dpvValue>` using W3C Data Privacy Vocabulary values — you cannot request a token
  without declaring why you are asking.
- KYC Match returns **match indicators, not values**. Do not build anything that expects to receive
  the operator's stored address.
- Rate limit 25 rps across all Orange APIs; exhaustion is **403 with `code: 53`**.
- No idempotency key exists. These are reads, so retries are safe — but they are billed.
