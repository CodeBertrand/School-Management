# Agent Instructions

> Guidelines for AI coding agents (Claude Code, Cursor, Copilot) working on this project.

## 01 Purpose

This file gives AI agents the context, rules and commands needed to produce consistent, high-quality code for **School-Management**. Follow it strictly.

## 02 Before You Start

- [ ] Read `docs/PRD.md` to understand the product, business rules and scope.
- [ ] Read `docs/ARCHITECTURE.md` for the structure and data model.
- [ ] Read `docs/API_CONVENTIONS.md` before touching any endpoint.
- [ ] Read `docs/DESIGN_SYSTEM.md` before touching any UI.
- [ ] Read `docs/SECURITY.md` before touching auth, roles or personal data.
- [ ] Check existing code and patterns before creating something new.
- [ ] Look at open issues and the current branch.

## 03 General Rules

- Backend: **Java 21, Spring Boot 3, Maven**. Frontend: **React 18, TypeScript (strict), Vite, Tailwind**.
- All code, comments, commit messages and docs are written in **English**.
- Follow the module structure in `ARCHITECTURE.md`. Do not create new top-level packages without a reason.
- Reuse existing services, components and utilities.
- Keep functions small and single-purpose.
- Add comments only for non-obvious business logic (for example the grade calculation).
- Do not create unnecessary files. Do not add dependencies without justification.
- Business rules in `PRD.md` (section 06) are the source of truth. Never change them silently.

## 04 Code Guidelines

**Backend**
- Controllers are thin: validate input, call a service, return a DTO.
- Never return JPA entities from controllers. Use DTOs (Java records) and MapStruct.
- Use constructor injection. No field injection.
- Business logic lives in services, never in controllers or repositories.
- Schema changes go through new Flyway migrations. Never edit an applied migration.
- Money is `long` (XAF). Grades are `BigDecimal` with scale 2.
- Throw domain exceptions; the global handler converts them to `ProblemDetail`.

**Frontend**
- Functional components and hooks only.
- Use TanStack Query for server state, React Hook Form + Zod for forms.
- Use components from `components/ui` (shadcn/ui) before creating new ones.
- Handle loading, error and empty states on every data view.
- Mobile first, accessible, semantic HTML.
- No `any`. Type API responses.

## 05 Security and Best Practices

- Never hardcode secrets or commit `.env` files.
- Use environment variables; keep `.env.example` up to date.
- Validate all input (Jakarta Validation on the backend, Zod on the frontend).
- Enforce authorization on the backend for every endpoint (`@PreAuthorize` plus ownership checks).
- Never log passwords, tokens or personal data of students.
- Follow `docs/SECURITY.md`.

## 06 Useful Commands

```bash
# Whole stack
docker compose up --build

# Backend (from /backend)
./mvnw spring-boot:run        # run the API
./mvnw test                   # unit tests
./mvnw verify                 # unit + integration tests + coverage

# Frontend (from /frontend)
npm install
npm run dev                   # dev server
npm run lint                  # lint
npm run test                  # unit tests
npm run build                 # production build
```

## 07 Definition of Done

- [ ] Feature matches the PRD and API conventions
- [ ] Tests written and passing (`./mvnw verify`, `npm run test`)
- [ ] Lint passes, no unused code
- [ ] Swagger annotations updated for new endpoints
- [ ] Docs updated if behavior or structure changed
- [ ] Commit message follows Conventional Commits

## 08 Need Help?

If unsure about something:
- Check the documentation in `docs/`.
- Look at existing code for the pattern to follow.
- Ask for clarification before making a big change.

Let's build something great.
