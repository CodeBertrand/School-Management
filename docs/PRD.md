# Product Requirements Document

> A clear plan for what we are building, why it matters, and how we will deliver it.

## 01 Product Overview

| | |
|---|---|
| **Product name** | School-Management |
| **Tagline** | Grades, report cards and fees, in one place. |
| **Description** | A web application and REST API that lets a school manage students, classes, grades, report cards and tuition fees, with role-based access for admins, teachers and parents. |
| **Scope** | One school per deployment (single-tenant). Multi-school is on the roadmap. |

## 02 Problem

Many private schools in Cameroon manage grades and fees with paper registers or spreadsheets. This causes:

- Calculation errors in averages and rankings.
- Slow, manual report card production at the end of every term.
- Disputes about fee payments because there is no reliable history.
- No way for parents to follow their child's results without visiting the school.

## 03 Goal

Let a school run a full term (grade entry, averages, ranking, report cards, fee tracking) with no spreadsheet, and let parents view results online.

## 04 Target Users

| Role | Need |
|---|---|
| **Admin** (principal, secretary, bursar) | "I want to set up the school year and produce all report cards without errors." |
| **Teacher** | "I want to enter my grades quickly and see my class statistics." |
| **Parent** | "I want to see my child's grades and what I still owe, without going to the school." |

## 05 Core Features (MVP)

1. **Users and roles**: login, JWT sessions, ADMIN / TEACHER / PARENT.
2. **Academic structure**: school year, 3 terms, 6 sequences, classes, subjects with coefficients, teacher assignment per class and subject.
3. **Students**: records, matricule (student ID), enrollment in a class, parent links.
4. **Grades**: entry per student, subject and sequence (out of 20), validation and audit trail.
5. **Report cards**: sequence and term averages, class rank, class average, appreciation, PDF export.
6. **Fees**: fee structures per level, payments (cash, Mobile Money reference, bank), balance per student.

## 06 Business Rules

- Grades are decimals from 0 to 20 (two decimals max).
- **Sequence average** = sum(grade x coefficient) / sum(coefficients of graded subjects).
- **Term average** = mean of the two sequence averages of that term (if only one is available, use it and flag the report card as partial).
- **Class rank**: by term average, descending. Equal averages share the same rank.
- A teacher can enter or edit grades only for the class and subject assigned to them, and only while the sequence is **open**.
- Closing a sequence is an admin action. Grade changes after closing require an admin and are logged.
- Money is stored in XAF as integers (no decimals).
- A payment can never exceed the remaining balance of the fee it targets.

**Worked example (used as a test case)**

| Subject | Coefficient | Grade | Weighted |
|---|---|---|---|
| Mathematics | 4 | 14 | 56 |
| French | 3 | 12 | 36 |
| English | 2 | 16 | 32 |
| **Total** | **9** | | **124** |

Sequence average = 124 / 9 = **13.78 / 20**

## 07 User Stories

**Admin**
- As an admin, I create a school year with its terms and sequences.
- As an admin, I create classes, subjects, and assign teachers with coefficients.
- As an admin, I enroll students and link them to parents.
- As an admin, I close a sequence and generate all report cards of a class as PDF.
- As an admin, I define fees and record payments.

**Teacher**
- As a teacher, I see only my classes and subjects.
- As a teacher, I enter grades for a sequence and see the class average.

**Parent**
- As a parent, I see report cards and fee balance for my own children only.

## 08 Non-Functional Requirements

- API responses under 500 ms for standard reads on a school of 1,000 students.
- Works on slow mobile connections: frontend bundle under 300 KB gzipped for the first load.
- Responsive UI (mobile first, because most users are on phones).
- Full OpenAPI documentation.
- Setup from clone to running app in under 5 minutes with Docker.

## 09 Success Metrics

- 100% of MVP endpoints documented in Swagger and covered by at least one integration test.
- At least 70% test coverage on service classes (grading and fees at 90%).
- Demo deployed on Render with seeded sample data.
- README, docs and a short demo GIF available on the repository.

## 10 Out of Scope (Roadmap)

- Attendance and discipline tracking
- Timetable generation
- SMS / WhatsApp notifications
- Real Mobile Money integration (MTN MoMo, Orange Money)
- Multi-school (multi-tenant) support
- ML module: early detection of students at risk of failing
- Anglophone subsystem grading (A-F scale)

## 11 Milestones

| Day | Deliverable |
|---|---|
| 1 | Docs, repo, backend skeleton, auth, academic structure |
| 2 | Students, enrollment, grades, averages and ranking |
| 3 | Report card PDF, fees, frontend screens |
| 4 | Tests, CI, Docker, deployment, demo data, README polish |
