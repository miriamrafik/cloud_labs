# API Test Plan — Lab 4

## Purpose

This document defines the acceptance tests for the Lab 4 Issues API.
The API has not been implemented yet. These tests establish what
"working correctly" means and will serve as the acceptance checklist
when implementation is complete.

## Test Cases

| Test ID | Request                                                                                          | Expected Result                                                                                                                         |
| ------- | ------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| T01     | `GET /api/issues`                                                                                | `200 OK`; a JSON array is returned.                                                                                                     |
| T02     | `POST /api/issues` with valid `title`, `description`, and `priority`                             | `201 Created`; the complete created issue is returned, including server-managed fields.                                                 |
| T03     | `POST /api/issues` with a missing `title`                                                        | `400 Bad Request`; error code `VALIDATION_ERROR`.                                                                                       |
| T04     | `POST /api/issues` with invalid `priority = "Critical"`                                          | `400 Bad Request`; error code `VALIDATION_ERROR`.                                                                                       |
| T05     | `PATCH /api/issues/:id` with an existing issue and `status = "Resolved"`                         | `200 OK`; the returned issue has status `Resolved`.                                                                                     |
| T06     | `PATCH /api/issues/:id` with `status = "Deleted"`                                                | `400 Bad Request`; error code `VALIDATION_ERROR`.                                                                                       |
| T07     | `PATCH /api/issues/:id` using an unknown issue ID                                                | `404 Not Found`; error code `ISSUE_NOT_FOUND`.                                                                                          |
| T08     | `GET /api/issues` when no issues exist                                                           | `200 OK`; an empty JSON array (`[]`) is returned.                                                                                       |
| T09     | Send unexpected server-owned fields, such as `id`, `status`, or `createdAt`, in a client request | The server ignores or rejects these fields according to the API contract. The client cannot choose authoritative server-managed values. |

## Acceptance Criteria

- Successful requests return the documented HTTP status codes and
  response formats.
- Invalid or missing input produces a clear validation error with
  the code `VALIDATION_ERROR`.
- Requests referencing nonexistent issues return `ISSUE_NOT_FOUND`.
- Only supported priority and status values are accepted.
- Server-managed fields, including `id`, `status`, and `createdAt`,
  cannot be controlled by the client.
- Listing issues works both when issues exist and when the collection
  is empty.
- Returned issue objects contain the fields required by the API
  contract.

## Execution Notes

These tests are initially a specification and do not imply that the
API or automated test suite already exists.

Once the Lab 4 implementation is complete:

1. Implement each test using the project's chosen testing framework.
2. Run the tests against the API.
3. Record pass/fail results and investigate any deviations.
4. Treat all acceptance criteria as requirements unless the API
   contract is explicitly revised.
