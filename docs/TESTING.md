# Testing

> How we make sure grades, rankings and money are always correct.

## 01 Strategy

The most important logic (grade averages, ranking, fee balances, authorization) must be tested the most. We follow a testing pyramid:

| Level | Share | Tools | What it covers |
|---|---|---|---|
| Unit | about 70% | JUnit 5, Mockito, AssertJ / Vitest | Pure logic and services |
| Integration | about 25% | Spring Boot Test, Testcontainers, MockMvc | Controllers, security, repositories, Flyway migrations |
| End-to-end | about 5% | Playwright (optional) | Critical user journeys |

## 02 Coverage Targets

| Area | Target |
|---|---|
| `grading` and `fees` services | 90% |
| Other backend services | 70% |
| Frontend utilities and hooks | 70% |
| Overall | 70% minimum, enforced in CI via JaCoCo |

Coverage is a floor, not a goal. A meaningful test beats a high number.

## 03 Backend Rules

- Test names: `method_condition_expectedResult`, for example `computeSequenceAverage_withMissingGrade_ignoresUngradedSubject`.
- Arrange / Act / Assert structure, one behavior per test.
- Integration tests use **Testcontainers PostgreSQL** (same engine as production, never H2).
- Flyway runs real migrations in integration tests, so schema errors are caught early.
- Use test data builders instead of copy-pasted setup.
- Security tests use `@WithMockUser` and real JWT flows for critical paths.

## 04 Must-Have Test Cases

**Grade calculation**
- Worked example: Math (coef 4, 14), French (coef 3, 12), English (coef 2, 16) gives **13.78**.
- Missing grade in one subject: that subject is ignored in both numerator and denominator.
- All grades missing: no average, no exception.
- Rounding `HALF_UP` to 2 decimals, applied only at the end.
- Term average with one sequence only is flagged as partial.

**Ranking**
- Distinct averages give ranks 1, 2, 3.
- Equal averages share a rank (1, 2, 2, 4).
- Students without an average are not ranked.

**Grade entry rules**
- Score below 0 or above 20 is rejected.
- Teacher cannot grade a class or subject they are not assigned to.
- Grade entry on a closed sequence is rejected for teachers, allowed and audited for admins.
- Duplicate grade (same student, subject, sequence) is rejected or updates the existing one.

**Fees**
- Payment above the remaining balance is rejected.
- Balance = total fees minus sum of payments.
- Amounts are integers in XAF.

**Authorization**
- Parent can read only their own children's report cards and balances (`403` otherwise).
- Teacher cannot create users or fee structures.
- Unauthenticated requests get `401`.
- Expired and tampered tokens are rejected.

## 05 Frontend Tests

- Vitest + React Testing Library: test behavior the user sees, not implementation details.
- Priority components: `GradeInput` validation, login form, report card view, fee balance display.
- Mock the API with MSW (Mock Service Worker).
- Check loading, error and empty states for each data view.

## 06 Commands

```bash
# Backend (from /backend)
./mvnw test          # unit tests
./mvnw verify        # unit + integration + JaCoCo report (target/site/jacoco)

# Frontend (from /frontend)
npm run test         # unit tests
npm run test -- --coverage
```

## 07 Continuous Integration

GitHub Actions runs on every push and pull request:
1. Backend: `./mvnw verify` with Testcontainers.
2. Frontend: `npm ci`, `npm run lint`, `npm run test`, `npm run build`.
3. Coverage threshold check.

A pull request cannot be merged if CI fails.

## 08 Manual Smoke Test (before a release)

- [ ] Login as admin, teacher and parent
- [ ] Create year, class, subject, student, enrollment
- [ ] Enter grades as teacher, check averages and rank
- [ ] Close sequence, generate report card PDF
- [ ] Record a payment, check balance
- [ ] Parent sees only their own child
