# Code Style

> Conventions that keep the codebase consistent and easy to review.

## 01 General

- Language of code, comments, commits and docs: **English**.
- Readability over cleverness. Small functions, clear names.
- Delete dead code instead of commenting it out.
- Formatting is automated: **Spotless** (Google Java Format) for the backend, **Prettier + ESLint** for the frontend. Run them before committing.

## 02 Backend (Java / Spring Boot)

**Naming**

| Element | Convention | Example |
|---|---|---|
| Package | lowercase, by module | `com.schoolmanagement.grading` |
| Class | PascalCase | `GradeService` |
| Method / variable | camelCase | `computeSequenceAverage` |
| Constant | UPPER_SNAKE_CASE | `MAX_GRADE` |
| DTO | record, suffix `Request` / `Response` | `CreateGradeRequest` |
| Entity | singular noun, no suffix | `Grade` |
| Table / column | snake_case, plural table | `school_classes`, `enrolled_at` |
| Flyway file | `V{n}__{description}.sql` | `V3__create_grades.sql` |

**Rules**
- Layering: `controller` to `service` to `repository`. Controllers never call repositories.
- Constructor injection with `final` fields (Lombok `@RequiredArgsConstructor`).
- DTOs are Java `record`s. Entities are never exposed.
- Use `Optional` for absence, never return `null` collections.
- Domain exceptions (`ResourceNotFoundException`, `BusinessRuleException`) handled by one `@RestControllerAdvice`.
- `@Transactional` on service methods that write.
- Logging with SLF4J: `log.info("Grade saved gradeId={}", id)`. No `System.out`.
- Public service methods have clear names that describe the business action.
- No business constants hidden in code: coefficients and limits come from data or named constants.

## 03 Frontend (React / TypeScript)

| Element | Convention | Example |
|---|---|---|
| Component file | PascalCase | `GradeTable.tsx` |
| Hook | `use` prefix, camelCase | `useClassGrades.ts` |
| Variable / function | camelCase | `selectedSequence` |
| Type / interface | PascalCase | `ReportCard` |
| Folder | kebab-case | `features/report-cards` |

**Rules**
- Functional components and hooks only.
- TypeScript `strict`. No `any`; use `unknown` and narrow.
- One component per file; keep components under about 150 lines, extract hooks and sub-components.
- Server state with TanStack Query; local UI state with `useState`. Avoid global stores unless needed.
- Forms: React Hook Form + Zod schema.
- Styling: Tailwind utility classes and design tokens from `DESIGN_SYSTEM.md`. No inline styles.
- Every data view handles loading, error and empty states.
- Accessible by default: labels, `aria-*`, keyboard support.

## 04 Git Workflow

**Branches**
- `main`: stable, always deployable.
- `feat/<short-name>`, `fix/<short-name>`, `docs/<short-name>`, `chore/<short-name>`.

**Commits**: [Conventional Commits](https://www.conventionalcommits.org)

```
feat(grading): compute term average and class rank
fix(fees): prevent payment above remaining balance
docs: add API conventions
test(grading): cover tie-breaking in ranking
chore: bump spring boot to 3.3.x
```

Allowed types: `feat`, `fix`, `docs`, `test`, `refactor`, `chore`, `ci`, `build`.
One logical change per commit. Imperative mood, under 72 characters for the subject.

**Pull requests**
- One PR per feature or module, linked to an issue (`Closes #12`).
- Small and focused. Squash merge into `main`.

PR checklist:
- [ ] Builds and tests pass in CI
- [ ] New logic has tests
- [ ] No secrets, no debug code
- [ ] Docs and Swagger updated
- [ ] Screenshots for UI changes

## 05 Versioning

Semantic Versioning (`v0.1.0`, `v0.2.0`, ...) with GitHub Releases and a `CHANGELOG.md`.

## 06 Project Hygiene

- `.gitignore`: `target/`, `node_modules/`, `.env`, IDE files.
- Keep `.env.example` in sync with every new variable.
- Remove unused imports and dependencies.
