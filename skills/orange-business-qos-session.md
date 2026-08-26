---
name: orange-business-qos-session
description: Raise network quality for a specific device-to-server flow for a bounded window, then hand it back — create, monitor, extend and terminate an Orange Quality-on-Demand session.
api: Orange Business CAMARA Network APIs
generated: '2026-08-26'
method: generated
source: openapi/_original/orange-business-quality-on-demand-openapi.yml
operations:
  - createSession
  - getSession
  - extendQosSessionDuration
  - deleteSession
  - retrieveSessionsByDevice
---

# Buy priority network quality for a bounded window

Quality on Demand is the one CAMARA API in this estate that creates real, billable, stateful
resources. Treat it as a write API, because it is one.

## Steps

1. **Create the session.** `createSession` (`POST /sessions`) with the device, the application
   server, a `qosProfile` name, and a `duration` in seconds. Optionally pass a `sink` URL and
   `sinkCredential` to receive events.

   The response carries `qosStatus`:
   - `AVAILABLE` — the session is live now.
   - `REQUESTED` — the network has not confirmed yet. You will get a
     `org.camaraproject.quality-on-demand.v1.qos-status-changed` CloudEvent when it resolves, either
     to `AVAILABLE`, or to `UNAVAILABLE` with `statusInfo: NETWORK_TERMINATED` if the network could
     not provide it.

   **`REQUESTED` is not success.** Do not report the session as active until you have seen
   `AVAILABLE`.

2. **Check state.** `getSession` (`GET /sessions/{sessionId}`). The session must have been created by
   the same API client the token identifies.

3. **Extend, while it is still alive.** `extendQosSessionDuration`
   (`POST /sessions/{sessionId}/extend`) with `requestedAdditionalDuration`. It works only while
   `qosStatus` is `AVAILABLE`. The total is capped at the QoS Profile's `maxDuration` — Orange's own
   example: a profile capped at 50,000s, a session created at 30,000s, an extension request of
   30,000s, resulting overall duration 50,000s, not 60,000s. Extending an expired session raises
   `QUALITY_ON_DEMAND.SESSION_EXTENSION_NOT_ALLOWED` (409).

4. **Hand it back.** `deleteSession` (`DELETE /sessions/{sessionId}`). This is the reversal path, and
   its window is stated: **before the planned end time**. If the session was `AVAILABLE` and you
   supplied a callback, you also receive a `qos-status-changed` event with `qosStatus: UNAVAILABLE`
   and `statusInfo: DELETE_REQUESTED`. No event fires if it was already `UNAVAILABLE`.

5. **Reconcile.** `retrieveSessionsByDevice` (`POST /retrieve-sessions`) lists every session for a
   device. Run this before creating a new one — there is no idempotency key, so a retried
   `createSession` after a timeout can leave you paying for two sessions.

## Reversibility

| action | reversible? | window |
|---|---|---|
| `createSession` | yes, via `deleteSession` | any time before the planned end time |
| `extendQosSessionDuration` | no | there is no "un-extend"; the duration is capped, not refunded |
| `deleteSession` | no | the session is gone; create a new one |

Once the duration elapses the session ends on its own and there is nothing left to reverse.

## Errors

| code | status | meaning |
|---|---|---|
| `QUALITY_ON_DEMAND.DURATION_OUT_OF_RANGE` | 400 | Duration outside the profile's allowed range. |
| `QUALITY_ON_DEMAND.QOS_PROFILE_NOT_APPLICABLE` | 422 | That profile is not available for this session right now. |
| `QUALITY_ON_DEMAND.SESSION_EXTENSION_NOT_ALLOWED` | 409 | Session is not in a state that can be extended. |
| `PERMISSION_DENIED` | 403 | The token does not own this session. |
| `NOT_FOUND` | 404 | Unknown sessionId. |
| `GONE` | 410 | The session existed and no longer does. |
| `INVALID_SINK` | 400 | Your callback URL is not valid for the protocol — only HTTP sinks are accepted. |

## Callbacks

Register a `sink` and Orange pushes CloudEvents 1.0 to it. Only HTTP is allowed as a sink protocol.
The bearer token Orange presents on the callback is the one you supplied in `sinkCredential` —
verify it on every inbound event.
