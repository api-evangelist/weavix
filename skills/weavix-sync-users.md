---
name: Sync workforce users into weavix
description: Provision, update, and deactivate weavix users from an HR or workforce-management system, assigning them to sites, permission groups, and crafts.
api: openapi/weavix-rest-openapi.yml
operations: [listSites, listPermissionGroups, listCrafts, listUsers, createUser, updateUser, deleteUser]
---

# Sync workforce users into weavix

Keep weavix user records in step with an external HR/workforce system.

## Auth
Send the account API key in the `Authorization` header on every request (raw token, no `Bearer` prefix). Base URL `https://api.weavix.com`. See `authentication/weavix-authentication.yml`.

## Steps
1. Resolve the target site with `listSites` (`GET /track/facilities`) to get its `facilityId`.
2. Resolve permission groups for that site with `listPermissionGroups` (`GET /core/groups/{facilityId}`) to get the `groupId` you will assign as `facilityGroup`.
3. Resolve `listCrafts` (`GET /core/crafts`) to get `craftId` values to attach to users.
4. Get the current roster with `listUsers` (`GET /core/people/names`) to decide create vs update vs delete.
5. For a new hire, call `createUser` (`POST /core/people/new-user`) with required `firstName`, `lastName`, `group`, and `facilities[]` (each `facilityId` + `facilityGroup`), plus optional `crafts[]`, `email`, `phone`, `folderId`, and `locale` (ISO 639, defaults `en`). The user is created but must still be activated.
6. For a changed record, call `updateUser` (`PUT /core/people/{id}`) with only the keys that changed — it is a partial update.
7. For a departed worker, call `deleteUser` (`DELETE /core/people/{id}`).

## Rules
- `group` is `global-admin`, `none`, or a permission-group id; global admins do not require `facilities`.
- Reuse existing ids; weavix ignores email-address capitalization when matching existing users.
- Errors surface as HTTP status codes; a `401` means the token is missing/invalid or API access is not enabled (see `errors/weavix-problem-types.yml`).
