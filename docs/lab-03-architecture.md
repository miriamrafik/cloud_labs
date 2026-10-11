# Lab 3 — Cloud Issue Tracker Architecture

## 1. Application Goal

The Cloud Issue Tracker is a web application that allows users to
create issues, view all recorded issues, and change the status of
existing issues.

The application uses a frontend to collect user input and display
issues, a REST API to process requests and enforce validation rules,
and a data layer to store and retrieve issue records.

The architecture separates the frontend, API, and data layer so each
component has a clear responsibility. The API acts as the trusted
entry point for all operations involving issue data.

## 2. Functional Scope

The application supports three primary operations:

1. **Create an issue**
   - Users submit an issue with a title and description.
   - The API validates the submitted data before storing it.
   - The data layer assigns an authoritative ID and initializes the
     issue status to `open`.
   - The API returns `201 Created` and the complete created issue
     as JSON.

2. **View all issues**
   - The frontend requests all issues from `GET /api/issues`.
   - The API retrieves the stored issues from the data layer.
   - The API returns `200 OK` with a JSON array.
   - If no issues exist, the API returns an empty array (`[]`).
   - The frontend displays the issues or a "No issues yet" message.

3. **Change an issue status**
   - Users select a new status for an existing issue.
   - The frontend sends a PATCH request to
     `/api/issues/{id}/status`.
   - The API validates the issue ID and the requested status.
   - The data layer updates only the status field of the issue.
   - The API returns `200 OK` and the complete updated issue as JSON.
   - The frontend updates the displayed issue.

The API accepts only supported status values, such as `open`,
`in_progress`, and `resolved`. Unsupported values are rejected with
`400 Bad Request` and a `VALIDATION_ERROR` response. Requests for
unknown issue IDs return `404 Not Found` with an `ISSUE_NOT_FOUND`
error code.

## 3. Request Flow

### Create Issue

```text
User
  |
  v
Frontend form
  |
  | POST /api/issues
  | JSON: { "title": "...", "description": "...", "priority": "..." }
  v
REST API
  |
  | Validate required fields and input constraints
  |
  +---- Invalid input ----> 400 Bad Request + VALIDATION_ERROR
  |
  v
Data layer
  |
  | Store issue, assign ID, initialize status to "open"
  v
REST API
  |
  | 201 Created + JSON issue
  v
Frontend
  |
  v
Render the newly created issue
Explanation:

The user enters a title and description in the frontend form and
submits the issue.

The frontend sends a POST /api/issues request with a JSON body.

The API validates the request, including the presence of a title
and applicable length limits.

If validation fails, the API returns 400 Bad Request with a
VALIDATION_ERROR response and does not create the issue.

If validation succeeds, the data layer stores the issue and
assigns its ID and initial open status. Server-managed fields
cannot be chosen authoritatively by the client.

The API returns 201 Created and the created issue as JSON.

The frontend adds the returned issue to the displayed list.

View All Issues
text
Page load
  |
  v
Frontend
  |
  | GET /api/issues
  v
REST API
  |
  v
Data layer
  |
  | Retrieve all issues
  v
REST API
  |
  | 200 OK + JSON array
  v
Frontend
  |
  +---- Array contains issues ----> Render issue cards or rows
  |
  +---- Array is empty ----------> Show "No issues yet"
Explanation:

When the page loads, the frontend sends GET /api/issues.

The API requests all stored issues from the data layer.

The API returns 200 OK and a JSON array.

If there are no issues, the response body is an empty JSON array,
not an error.

The frontend renders each issue as a row or card, or displays
"No issues yet" when the array is empty.

Change Issue Status
text
User clicks status button
  |
  v
Frontend
  |
  | PATCH /api/issues/{id}/status
  | JSON: { "status": "in_progress" }
  v
REST API
  |
  | Validate issue ID and requested status
  |
  +---- Unknown ID --------> 404 Not Found + ISSUE_NOT_FOUND
  |
  +---- Invalid status ----> 400 Bad Request + VALIDATION_ERROR
  |
  v
Data layer
  |
  | Update only the issue's status
  v
REST API
  |
  | 200 OK + updated issue JSON
  v
Frontend
  |
  v
Re-render the updated issue
Explanation:

The user clicks a status button for an issue.

The frontend sends a PATCH /api/issues/{id}/status request with
a JSON body, such as {"status": "in_progress"}.

The API checks whether the issue exists. If it does not, the API
returns 404 Not Found with ISSUE_NOT_FOUND.

The API validates the requested status. If the value is not
supported, it returns 400 Bad Request with VALIDATION_ERROR.

If both checks pass, the data layer updates only the status field,
preserving the issue's other fields.

The API returns 200 OK and the full updated issue as JSON.

The frontend re-renders the issue with its new status.

4. Trust Boundaries
A trust boundary separates user-controlled input from application-
controlled processing and data. Every request received from the
browser must be treated as untrusted until the API validates it.

Trust Boundary Diagram
text
USER-CONTROLLED SIDE                 APPLICATION-CONTROLLED SIDE

Browser input
     |
     | HTTP request
     +------------------------------------>
                                      REST API
                                          |
                                          | Validate and authorize
                                          | request data
                                          v
                                      Data layer
                                          |
                                          v
     <------------------------------------+
     | JSON response
     |
Browser renders issue data
User-Controlled Side
The user-controlled side includes the browser, frontend forms,
buttons, and every HTTP request sent by the client.

Users can modify browser-side code and request payloads using
developer tools, command-line clients, or API testing tools.
Therefore, frontend validation alone cannot guarantee valid or safe
data.

All client-supplied values must be considered untrusted.

Application-Controlled Side
The application-controlled side contains the REST API and data layer.

The API validates incoming requests and enforces the application's
rules before allowing data to be created or changed. The data layer
stores and retrieves issue records through application-controlled
code.

The browser never accesses the data layer directly.

Security Rules at the Boundary
Validate every request on the server. Check required fields,
data types, length limits, allowed priority values, allowed status
values, and issue existence where applicable. Frontend validation
improves usability but is not a security control.

Protect server-managed fields. The server is authoritative
for fields such as id, status, and createdAt. Client requests
must not be allowed to override these fields. Unexpected
server-owned fields must be ignored or rejected consistently
with the API contract.

Keep the data layer private. The frontend communicates with
the data layer only through the REST API.

Return safe error responses. Validation and missing-resource
errors use documented status codes and error codes. Unexpected
internal errors must not expose stack traces, secrets, or
implementation details to the client.

Render issue text safely. The frontend displays issue titles
and descriptions as plain text rather than interpreting them as
HTML. This reduces the risk of cross-site scripting (XSS).

Update only permitted fields. Status-change requests modify
only the status field and must not overwrite unrelated issue data.

Enforce the API contract consistently. Successful requests
return the documented JSON structure and HTTP status codes.
Invalid input and unknown issue IDs return the appropriate
documented error responses.

Design Principle
The REST API is the enforcement point between untrusted client input
and application data. Keeping validation, business rules, and
data access on the server makes the application more predictable,
testable, and secure.


**Save the file as:** `docs/lab-03-architecture.md`

This specification also aligns with your Lab 4 API test plan: it defines the expected successful responses, validation failures, missing-issue behavior, and protection of server-managed fields before implementation begins.
```
