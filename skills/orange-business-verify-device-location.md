---
name: orange-business-verify-device-location
description: Confirm a handset is actually where a transaction claims it is, using Orange's network-side location verification and retrieval rather than a self-reported GPS coordinate.
api: Orange Business CAMARA Network APIs
generated: '2026-08-26'
method: generated
source: openapi/_original/orange-business-device-location-verification-openapi.yml, openapi/_original/orange-business-device-location-retrieval-openapi.yml, openapi/_original/orange-business-device-roaming-status-openapi.yml
operations:
  - verifyLocation
  - retrieveLocation
  - getRoamingStatus
---

# Verify where a device really is

The point of this flow is that the answer comes from the mobile network, not from the handset. An
app can lie about its GPS; it cannot lie about which cell it is attached to.

## Choose the right call

- `verifyLocation` (`POST /verify`, Device Location Verification) — you already have a claim.
  Send a `CIRCLE` area and ask "is the device inside it?" Returns a verification result rather
  than a coordinate, so you learn the least you need to know. **Prefer this one.**
- `retrieveLocation` (`POST /retrieve`, Device Location Retrieval) — you need the area itself.
  Returns the area the network can place the device in. More data, more privacy exposure.
- `getRoamingStatus` (`POST /retrieve`, Device Roaming Status) — country-level, and much cheaper as
  a signal. Often enough on its own: a transaction claiming Paris from a handset roaming in Japan
  does not need a precise fix to be refused.

## Steps

1. Get a token (see the SIM-swap skill — same gateway, same `client_credentials` flow).
2. Call `getRoamingStatus` first if country is all you need. It returns the roaming state and country
   code.
3. Call `verifyLocation` with `device` (phoneNumber in E.164, or the identifier bound to your
   3-legged token), the `area` as a `CIRCLE` with a centre and radius, and `maxAge` — the oldest
   location fix you are willing to accept, in seconds.
4. Only fall through to `retrieveLocation` if you genuinely need the area.

## The errors that carry meaning

These are not failures — they are the answer, and each one means something different:

| code | status | meaning |
|---|---|---|
| `LOCATION_VERIFICATION.AREA_NOT_COVERED` | 422 | Orange cannot cover that area. Not "device is elsewhere". |
| `LOCATION_VERIFICATION.INVALID_AREA` | 422 | The circle you asked for is too small for the network's resolution. Widen it. |
| `LOCATION_VERIFICATION.UNABLE_TO_FULFILL_MAX_AGE` | 422 | No fix fresh enough. Either accept a staler one or treat as unknown. |
| `LOCATION_VERIFICATION.UNABLE_TO_LOCATE` | 422 | The network cannot place the device at all. |
| `LOCATION_RETRIEVAL.UNABLE_TO_FULFILL_MAX_SURFACE` | 422 | The area Orange can give you is larger than you said you would accept. |
| `IDENTIFIER_NOT_FOUND` | 404 | Not an Orange line. |

`UNABLE_TO_LOCATE` is **not** a fraud signal. A device in a basement is indistinguishable from a
device that is switched off.

## Rehearsing it

- `+990100000000` — Paris, nominal
- `+990100000002` — roaming in Spain, located Madrid
- `+990100000007` — roaming in Japan, located Tokyo
- `+990100000012` — Reunion Island, and **not** classified as roaming (the case that breaks naive
  "roaming means abroad" logic)
- `+990100000003` — location unavailable

## Before you act

- Location is consent-bound: the purpose travels in the OAuth scope as `dpv:<dpvValue>`.
- Rate limit 25 rps; exhaustion arrives as **403 with `code: 53`**.
- No idempotency key, no `Retry-After` header, and no way to un-ask a location question. These are
  reads, so retrying is safe, but each retry is a billable call against a live network.
