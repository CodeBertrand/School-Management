# School-Management

> Full-stack school management system: students, classes, grades, report cards (PDF), tuition fees and role-based access.

![Java](https://img.shields.io/badge/Java-21-orange) ![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-6DB33F) ![React](https://img.shields.io/badge/React-18-61DAFB) ![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791) ![License](https://img.shields.io/badge/License-MIT-blue)

## 01 Overview

Many private schools in Cameroon still manage grades, report cards and fee payments on paper or in spreadsheets. **School-Management** replaces that with a secure web application where:

- **Admins** manage the school year, classes, subjects, students, teachers and fees.
- **Teachers** enter grades for their own classes and subjects.
- **Parents** consult their children's report cards and fee balance.

Report cards follow the Cameroonian system: 3 terms, 2 sequences per term, grades out of 20, weighted by subject coefficients, with class ranking.

## 02 Features (MVP)

- [x] Authentication with JWT (access + refresh tokens) and role-based access (ADMIN, TEACHER, PARENT)
- [ ] School years, terms, sequences, classes, subjects (with coefficients)
- [ ] Student records and enrollment
- [ ] Grade entry per sequence, automatic averages and class ranking
- [ ] Report card generation (JSON + PDF)
- [ ] Tuition fee structures, payments and balances (amounts in XAF)
- [ ] Interactive API documentation (Swagger UI)

See [`docs/PRD.md`](docs/PRD.md) for the full scope and roadmap.

## 03 Tech Stack

| Layer | Technology |
|---|---|
| Backend | Java 21, Spring Boot 3, Spring Security (JWT), Spring Data JPA, Flyway, MapStruct, springdoc-openapi, OpenPDF |
| Database | PostgreSQL 16 |
| Frontend | React 18, Vite, TypeScript, Tailwind CSS, shadcn/ui, TanStack Query, React Hook Form + Zod |
| Testing | JUnit 5, Mockito, Testcontainers, Vitest, React Testing Library |
| DevOps | Maven, Docker, docker-compose, GitHub Actions, Render |

## 04 Getting Started

### Prerequisites
- Java 21, Node.js 20+, Docker Desktop

### Run everything with Docker
```bash
git clone https://github.com/YOUR_USERNAME/School-Management.git
cd School-Management
cp .env.example .env
docker compose up --build
```
- API: http://localhost:8080
- Swagger UI: http://localhost:8080/swagger-ui.html
- Frontend: http://localhost:5173

### Run locally without Docker for the apps
```bash
# 1. Start only the database
docker compose up -d db

# 2. Backend
cd backend && ./mvnw spring-boot:run

# 3. Frontend (new terminal)
cd frontend && npm install && npm run dev
```

## 05 Project Structure

```
School-Management/
├── backend/            # Spring Boot API (Maven)
├── frontend/           # React + Vite app
├── docs/               # Project documentation
├── .github/workflows/  # CI pipelines
├── docker-compose.yml
├── render.yaml         # Render deployment blueprint
└── .env.example
```

## 06 Documentation

| Document | Purpose |
|---|---|
| [PRD](docs/PRD.md) | What we build and why |
| [ARCHITECTURE](docs/ARCHITECTURE.md) | Technical design and data model |
| [API_CONVENTIONS](docs/API_CONVENTIONS.md) | REST rules and endpoint catalog |
| [DESIGN_SYSTEM](docs/DESIGN_SYSTEM.md) | UI rules, colors, components |
| [SECURITY](docs/SECURITY.md) | Auth, roles and data protection |
| [CODE_STYLE](docs/CODE_STYLE.md) | Naming, structure and Git conventions |
| [TESTING](docs/TESTING.md) | Test strategy |
| [AGENTS](docs/AGENTS.md) | Instructions for AI coding assistants |

## 07 Roadmap

Attendance tracking, timetables, SMS/WhatsApp notifications to parents, Mobile Money payments, multi-school support, and an ML module to detect students at risk of failing.

## 08 License

MIT
