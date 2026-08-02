---
name: Send messages and retrieve channel history
description: Push a system message to a weavix channel or an individual worker, optionally capturing their reply, then pull channel message history for reporting.
api: openapi/weavix-rest-openapi.yml
operations: [listChannels, listUsers, sendChannelMessage, sendUserMessage, searchChannelMessages]
---

# Send messages and retrieve channel history

Deliver operational messages into weavix and pull history back out.

## Auth
Send the account API key in the `Authorization` header on every request. Base URL `https://api.weavix.com`.

## Steps
1. To message a channel, resolve `listChannels` (`GET /core/channels`) for the `channelId`, then `sendChannelMessage` (`POST /api/{channelId}/message`) with `message` (required), optional `senderName` (defaults to "weavix Integrations"), and `readAloud`.
2. To message one worker, resolve `listUsers` (`GET /core/people/names`) for the `userId`, then `sendUserMessage` (`POST /api/admin/channel-message`) with `userId` and `message`. Optionally set `senderName`, `messageLanguage` (ISO 639), `readAloud`, `mediaUrl`, `channelImageUrl`, and `responseUrl`.
3. If you set `responseUrl`, the worker's reply transcript is POSTed back to that URL — use it as a webhook callback (see `asyncapi/weavix-webhooks.yml`).
4. To pull history, call `searchChannelMessages` (`POST /external/channels/{channelId}/messages/search`) with `fromDate`/`toDate` (UTC), `contentTypes[]` (audio, image, video, file, link), and `pageSize` (max 1000). If a `next` cursor is returned, pass it back to page through all results.

## Rules
- `message` is required on every send; `senderName` controls the displayed sender.
- Message search is cursor-paginated via `pageSize` + `next` (see `conventions/weavix-conventions.yml`).
- Messages can be auto-translated into the recipient's language via `messageLanguage`.
