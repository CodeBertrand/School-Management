# API Conventions

> Rules every endpoint follows, and the catalog of the MVP API.

## 01 Basics

- Base path: `/api/v1`
- Format: JSON (`application/json`), UTF-8. PDF endpoints return `application/pdf`.
- Auth: `Authorization: Bearer <access_token>` on every endpoint except `/auth/login` and `/auth/refresh`.
- Documentation: OpenAPI at `/v3/api-docs`, Swagger UI at `/swagger-ui.html`.
- Dates: ISO 8601 (`2026-10-21`, `2026-10-21T09:30:00Z`).
- Money: integer in XAF, field suffix `Xaf` (for example `amountXaf: 150000`).
- Grades: decimal with 2 digits max, field name `score`.
- JSON field names: camelCase.

## 02 Resource Naming

- Plural nouns, kebab-case: `/school-years`, `/fee-structures`.
- Nested only one level for ownership: `/classes/{id}/students`.
- Actions that are not CRUD use a verb sub-resource: `POST /sequences/{id}/close`.

| Method | Meaning | Success code |
|---|---|---|
| GET | Read | 200 |
| POST | Create | 201 (+ `Location` header) |
| PUT | Replace / full update | 200 |
| PATCH | Partial update | 200 |
| DELETE | Delete (soft when history matters) | 204 |

## 03 Status Codes

| Code | When |
|---|---|
| 400 | Malformed request or validation error |
| 401 | Missing, invalid or expired token |
| 403 | Authenticated but not allowed |
| 404 | Resource not found (or hidden) |
| 409 | Conflict (duplicate grade, closed sequence) |
| 422 | Business rule violation (payment above balance) |
| 500 | Unexpected error (never leaks details) |

## 04 Error Format (RFC 7807 ProblemDetail)

```json
{
  "type": "https://school-management.dev/problems/validation-error",
  "title": "Validation failed",
  "status": 400,
  "detail": "One or more fields are invalid.",
  "instance": "/api/v1/grades",
  "code": "VALIDATION_ERROR",
  "errors": [
    { "field": "score", "message": "must be between 0 and 20" }
  ],
  "timestamp": "2026-10-21T09:30:00Z"
}
```

`code` is a stable machine-readable value the frontend can switch on (`VALIDATION_ERROR`, `SEQUENCE_CLOSED`, `PAYMENT_EXCEEDS_BALANCE`, `FORBIDDEN_RESOURCE`).

## 05 Pagination, Sorting, Filtering

Query parameters: `page` (starts at 0), `size` (default 20, max 100), `sort=field,asc|desc`.

```json
{
  "content": [ ],
  "page": 0,
  "size": 20,
  "totalElements": 134,
  "totalPages": 7
}
```

Filters are plain query params: `GET /students?classId=3&q=mbarga`.

## 06 Endpoint Catalog (MVP)

**Auth**

| Method | Path | Role | Description |
|---|---|---|---|
| POST | `/auth/login` | Public | Returns access and refresh tokens |
| POST | `/auth/refresh` | Public | Rotates refresh token |
| POST | `/auth/logout` | Any | Revokes refresh token |
| GET | `/auth/me` | Any | Current user profile |

**Users**

| Method | Path | Role | Description |
|---|---|---|---|
| POST | `/users` | ADMIN | Create teacher, parent or admin |
| GET | `/users` | ADMIN | List users (`?role=TEACHER`) |
| PATCH | `/users/{id}` | ADMIN | Update or deactivate |

**Academic structure**

| Method | Path | Role | Description |
|---|---|---|---|
| POST, GET | `/school-years` | ADMIN (GET: all) | Create or list school years |
| GET | `/school-years/{id}/terms` | Any | Terms and sequences of a year |
| POST | `/sequences/{id}/close` | ADMIN | Close a sequence |
| POST | `/sequences/{id}/reopen` | ADMIN | Reopen a sequence (audited) |
| POST, GET | `/classes` | ADMIN (GET: by role) | Create or list classes |
| GET | `/classes/{id}` | ADMIN, TEACHER | Class detail |
| POST | `/classes/{id}/subjects` | ADMIN | Add subject with coefficient and teacher |
| POST, GET | `/subjects` | ADMIN | Manage subject catalog |

**Students**

| Method | Path | Role | Description |
|---|---|---|---|
| POST | `/students` | ADMIN | Create student |
| GET | `/students` | ADMIN, TEACHER | List (teacher: own classes only) |
| GET | `/students/{id}` | ADMIN, TEACHER, PARENT (own) | Student detail |
| PUT | `/students/{id}` | ADMIN | Update |
| POST | `/students/{id}/parents` | ADMIN | Link a parent account |
| POST | `/enrollments` | ADMIN | Enroll a student in a class |
| GET | `/classes/{id}/students` | ADMIN, TEACHER | Students of a class |

**Grades and report cards**

| Method | Path | Role | Description |
|---|---|---|---|
| PUT | `/classes/{classId}/subjects/{subjectId}/sequences/{sequenceId}/grades` | ADMIN, TEACHER | Bulk upsert of grades for a whole class |
| GET | `/classes/{classId}/grades?sequenceId=&subjectId=` | ADMIN, TEACHER | Grade grid |
| GET | `/students/{id}/report-card?termId=` | ADMIN, TEACHER, PARENT (own) | Report card as JSON |
| GET | `/students/{id}/report-card.pdf?termId=` | ADMIN, TEACHER, PARENT (own) | Report card as PDF |
| GET | `/classes/{id}/ranking?termId=` | ADMIN, TEACHER | Class ranking |

**Fees**

| Method | Path | Role | Description |
|---|---|---|---|
| POST, GET | `/fee-structures` | ADMIN | Define fees per level and year |
| POST | `/payments` | ADMIN | Record a payment |
| GET | `/payments?studentId=` | ADMIN, PARENT (own) | Payment history |
| GET | `/students/{id}/balance` | ADMIN, PARENT (own) | Total due, paid, remaining |

## 07 Example: Report Card Response

```json
{
  "student": { "id": 12, "matricule": "SM-2026-0012", "fullName": "Marie Nkomo" },
  "class": { "id": 3, "name": "Form 5 A" },
  "term": { "id": 1, "number": 1 },
  "subjects": [
    { "subject": "Mathematics", "coefficient": 4, "seq1": 14.00, "seq2": 15.00, "termAverage": 14.50 },
    { "subject": "French", "coefficient": 3, "seq1": 12.00, "seq2": 13.00, "termAverage": 12.50 }
  ],
  "termAverage": 13.64,
  "classAverage": 11.20,
  "rank": 3,
  "classSize": 42,
  "appreciation": "Good work",
  "partial": false
}
```

## 08 Versioning and Changes

- Breaking changes go to `/api/v2`. Additive changes (new optional fields) stay in `v1`.
- Every new endpoint must have: OpenAPI annotations, request validation, authorization rule, and an integration test.
