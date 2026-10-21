# Design System

> A consistent, accessible and mobile-first design system for School-Management.

## 01 Brand Identity

- **Name**: School-Management
- **Personality**: trustworthy, clear, calm. This is a tool for administrators, teachers and parents, so clarity beats decoration.
- **Principles**: mobile first, readable tables, strong contrast, minimal clicks to the most common action (enter a grade, view a report card).

## 02 Color Palette

| Token | Hex | Usage |
|---|---|---|
| `primary` | `#4F46E5` | Main buttons, links, active navigation |
| `primary-foreground` | `#FFFFFF` | Text on primary |
| `secondary` | `#0D9488` | Secondary actions, highlights |
| `accent` | `#F59E0B` | Badges, important callouts |
| `background` | `#F8FAFC` | Page background |
| `surface` | `#FFFFFF` | Cards, tables, dialogs |
| `border` | `#E2E8F0` | Dividers, inputs |
| `text` | `#0F172A` | Main text |
| `muted` | `#64748B` | Secondary text, placeholders |
| `success` | `#16A34A` | Paid, passed, saved |
| `warning` | `#D97706` | Partial, pending |
| `error` | `#DC2626` | Errors, unpaid, failed |
| `info` | `#2563EB` | Informational messages |

Rules: never use color alone to convey meaning (add an icon or text). Text contrast must be at least 4.5:1 (WCAG AA). Dark mode is out of scope for MVP, but colors are defined as CSS variables to allow it later.

## 03 Typography

Font: **Inter** (fallback: system-ui, sans-serif).

| Style | Size | Line height | Weight |
|---|---|---|---|
| H1 | 32px | 40px | Bold |
| H2 | 24px | 32px | Semibold |
| H3 | 20px | 28px | Semibold |
| Body | 16px | 24px | Regular |
| Small | 14px | 20px | Regular |
| Caption | 12px | 16px | Regular |

## 04 Spacing

8px base grid: `4, 8, 12, 16, 24, 32, 48, 64`. Use Tailwind scale (`p-2`, `p-4`, `gap-6`). Page gutter is 16px on mobile and 24px from tablet up.

## 05 Border Radius

| Token | Value | Usage |
|---|---|---|
| `sm` | 4px | Badges, small chips |
| `md` | 8px | Inputs, buttons |
| `lg` | 12px | Cards, dialogs |
| `full` | 9999px | Avatars, pills |

## 06 Components

Built on **shadcn/ui** (Radix + Tailwind). Reuse before creating.

- **Button**: Primary, Secondary, Ghost, Destructive. Minimum height 40px (44px on mobile touch targets).
- **Input / Select / Textarea** with label above, helper text and inline error message.
- **Badge**: Default, Success (paid), Warning (partial), Error (unpaid), Info.
- **Card**: surface with `lg` radius and subtle border.
- **DataTable**: sortable, paginated, sticky header; on mobile switches to stacked cards.
- **Dialog / Sheet** for forms and confirmations.
- **Toast** for success and error feedback.
- **Skeleton** for loading, **EmptyState** with an action for empty lists.
- **GradeInput**: numeric, 0 to 20, step 0.25, highlights invalid values immediately.

## 07 Icons

**Lucide React**. Size 16px inline, 20px in buttons, 24px in navigation. Always pair icon-only buttons with an `aria-label`.

## 08 Responsive Breakpoints

| Name | Width | Layout |
|---|---|---|
| Mobile | below 640px | Single column, bottom or drawer navigation |
| Tablet | 640 to 1023px | Two columns, collapsible sidebar |
| Laptop | 1024 to 1439px | Fixed sidebar and content |
| Desktop | 1440px and above | Wider content, max width 1280px |

## 09 Key Screens

1. Login
2. Dashboard (role-based: admin overview, teacher classes, parent children)
3. Classes and class detail (students, subjects)
4. Grade entry grid (students as rows, one sequence and subject at a time)
5. Report card view with PDF download
6. Fees: student balance, payment history, record payment

## 10 Accessibility Checklist

- [ ] Keyboard navigable, visible focus ring
- [ ] Labels linked to inputs
- [ ] Contrast AA minimum
- [ ] Error messages announced (`aria-live`)
- [ ] Touch targets at least 44px on mobile
