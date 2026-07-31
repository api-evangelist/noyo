---
name: Connect a group to a carrier
description: Create a group and request a carrier connection so member enrollment data can be exchanged, then confirm the connection status.
api: openapi/noyo-fulfillment-openapi.json
operations: [createGroup, createGroupConnection, createGroupConnectionWithExistingGroup, getGroupConnection, getGroup]
---

# Connect a group to a carrier

Establish the group-carrier relationship that lets Noyo exchange enrollment data.

## Auth
Bearer token from `POST https://accounts.noyo.com/auth/public/token`
(client-credentials; see authentication/noyo-authentication.yml).

## Steps
1. **Create the group** — `createGroup` (skip if the group already exists; fetch
   with `getGroup`).
2. **Request the connection** — `createGroupConnection` for a brand-new group, or
   `createGroupConnectionWithExistingGroup` to add a carrier to an existing
   group.
3. **Confirm status** — `getGroupConnection` returns the latest connection status.
   In sandbox, new connections stay in `connecting` and must be advanced by the
   Noyo team; in production, watch for the `group_connection_request.status_change`
   webhook.

## Rules
- Carriers enforce different authorization models — review
  https://docs.noyo.com/docs/groups/connections/authorization before connecting.
- Errors are `application/json`; 403 can indicate insufficient permissions on a
  protected group. See errors/noyo-problem-types.yml.
