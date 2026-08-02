---
name: Broadcast a targeted mass alert
description: Trigger a weavix mass alert to a facility, optionally narrowed to specific crafts, geofences, or on-site workers, then review the alert history.
api: openapi/weavix-rest-openapi.yml
operations: [listSites, listAlertTypes, sendMassAlert, listFacilityAlerts]
---

# Broadcast a targeted mass alert

Send a safety-critical mass alert that is read aloud and translated on each worker's walt.

## Auth
Send the account API key in the `Authorization` header on every request. Base URL `https://api.weavix.com`.

## Steps
1. Resolve the target facility with `listSites` (`GET /track/facilities`) for its `facilityId`.
2. Resolve `listAlertTypes` (`GET /core/alert-types`) to get the `alertTypeId` to invoke.
3. Send with `sendMassAlert` (`POST /track/facility-alerts/{facilityId}`) passing `alertTypeId` and `message` (required). Optionally target with `crafts[]`, and a `location` object: `currentlyIn: true` limits to workers on site (repeat the `facilityId` in `location.facilityIds`), and `geofenceIds[]` limits to workers reporting a location inside those geofences.
4. Confirm and audit with `listFacilityAlerts` (`GET /track/facility-alerts/{facilityId}`).

## Rules
- The `message` is read aloud on walts and auto-translated into each worker's set language.
- This is a high-consequence, safety-critical action (see `agentic-access/weavix-agentic-access.yml`); confirm the facility, alert type, and targeting before sending.
- Omitting `crafts`/`geofenceIds` broadcasts to all matching users at the facility.
