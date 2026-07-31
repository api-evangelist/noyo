---
name: Enroll a new hire in coverage
description: Create a new employee for a group and enroll them (and optional dependents) in carrier coverage via a new-hire member request, then track the resulting change to carrier confirmation.
api: openapi/noyo-fulfillment-openapi.json
operations: [createEmployee, createDependent, newHireEnrollmentMemberRequest, getMemberRequest, getEmployeeMemberRequestList]
---

# Enroll a new hire

Use this skill to onboard a newly hired employee into benefits coverage through Noyo.

## Auth
Exchange your `CLIENT_ID`/`CLIENT_SECRET` (HTTP Basic) for a short-lived bearer
token at `POST https://accounts.noyo.com/auth/public/token` with body
`{"grant_type":"client_credentials"}`. Send `Authorization: Bearer <ACCESS_TOKEN>`
on every call. Use sandbox keys against `https://fulfillment-sandbox.noyo.com`
before going live on `https://fulfillment.noyo.com`.

## Steps
1. **Create the employee** — `createEmployee` under the target group. Save the
   returned Noyo employee `id` (and link your own `custom_individual_id` if you
   track one).
2. **Add dependents (optional)** — `createDependent` for each dependent of the
   employee.
3. **Submit the new-hire request** — `newHireEnrollmentMemberRequest` to add the
   employee and optional dependents to one or more carrier coverages.
4. **Poll the request** — `getMemberRequest` (or `getEmployeeMemberRequestList`)
   to follow fulfillment status; each request may spawn one or more member
   transactions.

## Rules
- Datetimes are UTC; integer datetimes are Unix epoch seconds.
- List endpoints paginate with `page_size` + `offset`.
- Errors are `application/json` (see errors/noyo-problem-types.yml): 422 =
  validation error, 409 = version conflict, 404 = unknown group/member.
- Prefer webhooks (`member_snapshot.status_change`, `difference.created`) over
  tight polling — see asyncapi/noyo-webhooks.yml.
