# Security

> How we protect accounts, student data and money records.

## 01 Why It Matters

The system stores personal data about **minors** (names, birth dates, grades) and financial records. A leak or an unauthorized grade change causes real harm. Security is a feature, not an afterthought.

## 02 Authentication

- Login with email and password: `POST /api/v1/auth/login`.
- Passwords hashed with **BCrypt** (strength 12). Never stored or logged in clear text.
- **Access token**: JWT, 15 minutes. **Refresh token**: 7 days, stored hashed in the database, revocable, rotated on use.
- JWT signed with HS256 using `JWT_SECRET` (at least 32 random bytes, from environment only).
- Claims: `sub` (user id), `role`, `exp`. No personal data in the token.
- Login rate limiting: 5 failed attempts per 15 minutes per account and IP, then temporary lock.
- Password policy: at least 10 characters, checked against a small list of common passwords.
- Initial admin created through a seed command, with a forced password change at first login.

## 03 Authorization (RBAC)

| Resource / Action | ADMIN | TEACHER | PARENT |
|---|:-:|:-:|:-:|
| Manage users, years, classes, subjects | Yes | No | No |
| Manage students and enrollments | Yes | Read (own classes) | No |
| Enter or edit grades | Yes (audited) | Own class and subject, open sequence only | No |
| Close or reopen a sequence | Yes | No | No |
| View report card | Yes | Own classes | Own children only |
| Manage fee structures | Yes | No | No |
| Record a payment | Yes | No | No |
| View fee balance | Yes | No | Own children only |

Rules:
- Roles are enforced on the backend with `@PreAuthorize`. Hiding a button in the UI is never security.
- **Ownership checks** are mandatory: a parent requesting another child's report card gets `403`, even with a valid token.
- Return `404` instead of `403` when revealing existence would leak information.

## 04 Data Protection

- HTTPS only in production (provided by Render). Enable HSTS.
- Database credentials and secrets only through environment variables. `.env` is git-ignored; `.env.example` has placeholders only.
- Never log: passwords, tokens, full personal records. Log ids instead.
- API responses expose only the fields the role needs (dedicated DTOs per use case).
- Deleting a student is a **soft delete** to keep financial and academic history.
- Backups: database backup before each production migration.

## 05 Input Validation

- Backend: Jakarta Validation on every request DTO (`@NotBlank`, `@Size`, `@DecimalMin("0")`, `@DecimalMax("20")`).
- Frontend: Zod schemas mirror backend rules (convenience only, never trusted).
- Use JPA / parameterized queries only. No string concatenation in queries.
- Limit page size (max 100) and request body size.

## 06 Web Security

- **CORS**: allow only the configured frontend origin (`CORS_ALLOWED_ORIGINS`).
- Security headers: `X-Content-Type-Options`, `X-Frame-Options: DENY`, `Referrer-Policy`, basic CSP on the frontend.
- Frontend token storage: access token in memory, refresh token in an `HttpOnly`, `Secure`, `SameSite=Strict` cookie. Never in `localStorage`.
- Errors never expose stack traces or SQL details (`ProblemDetail` with a safe message).

## 07 Audit Trail

Every grade creation, update, deletion and every payment writes an `audit_log` entry: actor, action, entity, entity id, old value, new value, timestamp. Audit entries are append-only.

## 08 Dependencies and CI

- Dependabot enabled for Maven and npm.
- CI runs tests and a dependency vulnerability check (OWASP Dependency-Check or `npm audit`).
- Pin major versions; review updates before merging.

## 09 Security Checklist (before each release)

- [ ] No secrets in the repository or its history
- [ ] All endpoints have explicit authorization rules
- [ ] Ownership checks tested (parent A cannot read parent B's child)
- [ ] Login rate limiting active
- [ ] Production `JWT_SECRET` is unique and random
- [ ] CORS restricted to the real frontend URL
- [ ] Swagger UI disabled or protected in production if the data is real
- [ ] Dependencies scanned

## 10 Reporting a Vulnerability

Open a private security advisory on GitHub (Security tab) or contact the maintainer by email. Do not publish details publicly before a fix is available.
