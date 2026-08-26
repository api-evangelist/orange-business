---
name: orange-business-live-objects-onboard-device
description: Register a device on Orange Live Objects, give it a scoped API key, route its data to your systems, and verify the route works before any hardware ships.
api: Orange Business Live Objects
generated: '2026-08-26'
method: generated
source: openapi/orange-business-live-objects-openapi.json
operations:
  - createDeviceV1
  - getDeviceV1
  - listDevicesV1
  - createGroup
  - addInterfaceToDevice
  - createApiKey
  - getApiKeyFromAuthentication
  - createNotificationChannel
  - createActionPolicyV1
  - setEnabledField
  - addDataMessage
  - dslQueryV1
  - startDebugMode
---

# Onboard a device on Live Objects and route its data

Live Objects is at `https://liveobjects.orange-business.com`, contract version 2026.7.0, 263
operations. Authenticate with `X-API-KEY` in the header, or OAuth 2.0 authorization code against
`https://liveobjects.orange-business.com/api/v1/oauth2/authorize` and `/oauth2/token`.

Every operation is gated by a role on the key. The roles you need here are `DEVICE_W`, `DATA_W`,
`DATA_R`, `API_KEY_W` and `DATA_PROCESSING_W`. The full list of 23 is in
`scopes/orange-business-scopes.yml`.

## Steps

1. **Group first.** `createGroup` (`POST /api/v1/deviceMgt/groups`). Groups nest via `parentId` and
   flatten into `groupPath`. Campaigns target groups, so getting the hierarchy right now saves
   re-homing devices later.

2. **Create the device.** `createDeviceV1` (`POST /api/v1/deviceMgt/devices`). Confirm with
   `getDeviceV1`; list with `listDevicesV1`.

3. **Attach an interface.** `addInterfaceToDevice`
   (`POST /api/v1/deviceMgt/devices/{deviceId}/interfaces`) binds the device to a connector node —
   this is where LoRa and cellular attachment is expressed.

4. **Mint a scoped key for the device.** `createApiKey` (`POST /api/v0/apiKeys`). Give it the
   narrowest role set that works: `DEVICE_ACCESS` for MQTT device mode, `DATA_W` for a device
   pushing over HTTPS. The key resource also carries its own rate limit
   (`mqttDeviceWindowSize` / `mqttDeviceMaxMessages` / `mqttBridgeWindowSize` /
   `mqttBridgeMaxMessages`) — read it back with `getApiKey` so you know the ceiling before you
   deploy a fleet. `getApiKeyFromAuthentication` (`GET /api/v0/apiKeys/current_key`) tells you what
   the key you are currently holding can do.

5. **Set up delivery.** `createNotificationChannel`
   (`POST /api/v1/contact/notificationChannels`), then `createActionPolicyV1`
   (`POST /api/v1/event2action/actionPolicies`) to say what fires and where it goes — HTTP push,
   FIFO queue, or a notification channel.

6. **Test the route before hardware exists.** This is the part worth using: Orange ships test
   endpoints for its own delivery layer.
   - `POST /api/v1/event2action/test/http-push` — fire a test push at your endpoint.
   - `POST /api/v1/event2action/test/fifo` — fire a test message onto a FIFO queue.
   Then check `GET /api/v1/event2action/diagnostics/actionPolicies/{policyId}` and the per-action
   metrics endpoint to see what actually happened. Use `setEnabledField`
   (`PUT /api/v1/event2action/actionPolicies/{policyId}/enabled`) to toggle a policy without
   deleting it.

7. **Push and read data.** `addDataMessage` (`POST /api/v0/data/streams/{streamId}`) or
   `addDataBulk`. Query with `dslQueryV1` (`POST /api/v1/data/search`).

8. **Debug a misbehaving device.** `startDebugMode`
   (`POST /api/v1/deviceMgt/devices/{deviceId}/interfaces/{interfaceId}/debug`), then
   `stopDebugMode` when done. Debug mode is a toggle, so it is fully reversible.

## Pagination

Three styles, and it matters which you pick:

- `page` + `size` — fine for small collections.
- `limit` + `offset` — **`offset` is deprecated above 10,000.**
- `bookmarkId` (+ `bookmarkEndpointName`) — the cursor. Use this for anything fleet-sized.

`X-Total-Count` is a **request** header here, not a response header: set it to `true` to opt in to a
total count.

## Reversibility — read this before you delete anything

| operation | reversible |
|---|---|
| `setEnabledField`, `startDebugMode`/`stopDebugMode`, `setApiKeyDebugMode`, decoder activate | yes — plain toggles |
| `cancelResourceUpdateV1` | cancels a resource push in flight |
| `cancelCampaign` | cancels a fleet campaign; Orange does not state up to which state |
| `deleteDeviceV1`, `deleteGroup`, `deleteApiKey`, `deleteResource`, `deletePipeline`, `deleteTwinObservations`, and 30+ other DELETEs | **no** |

There is no restore, undelete or trash endpoint anywhere in the 263-operation contract, and no
retention period is published. Treat every Live Objects DELETE as permanent, and confirm with a
human before issuing one.

## Errors

Live Objects uses `{ "id": "<uuid>", "code": "<STRING>", "message": "...", "details": "..." }`, and on
some operations adds `"category": "PERMANENT" | "TRANSIENT" | "UNEXPECTED"`. That `category` is the
only machine-readable retryability signal Orange publishes anywhere in the estate — retry
`TRANSIENT`, never retry `PERMANENT`. Quote the `id` when you contact support.

Note this is a **different** envelope from both the api.orange.com gateway
(`{code:<int>, message, description}`) and the CAMARA APIs (`{status, code:<string>, message}`).
A client spanning Orange's estates must parse all three.
