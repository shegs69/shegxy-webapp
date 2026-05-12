# Shegxy Todo App — Full Premium Makeover

Transform the basic AWS Serverless TODO starter into a polished, branded **Shegxy** productivity app with a premium dark-mode glassmorphic design, new features, and a modern dashboard layout.

## Decisions Made

> **Branding**: The app was renamed to **"Shegxy Tasks"** throughout.

> **Prisma Schema Changes**: Added `priority`, `dueDate`, and `category` fields to the `TodoItem` model. Requires a database migration against AWS Aurora (`npx prisma migrate dev`).

> **Tailwind CSS v4**: All styling uses Tailwind utility classes. The design system in `globals.css` was enhanced with custom CSS variables and animations.

> **Translate Feature**: Kept in the redesign.

> **Categories**: Implemented as free-form text tags.

---

## Proposed Changes

### 1. Database Schema — Prisma

#### [MODIFY] `schema.prisma`

Add new fields to the `TodoItem` model:

```diff
+enum TodoItemPriority {
+  LOW
+  MEDIUM
+  HIGH
+  URGENT
+}

 model TodoItem {
   id          String         @id @default(uuid())
   title       String
   description String         @db.Text()
   userId      String
   status      TodoItemStatus
+  priority    TodoItemPriority @default(MEDIUM)
+  dueDate     DateTime?
+  category    String?

   createdAt DateTime @default(now())
   updatedAt DateTime @updatedAt
```

---

### 2. Design System — CSS & Layout

#### [MODIFY] `globals.css`

Complete redesign of the CSS variables for a premium dark-first theme:
- **Color palette**: Deep navy/slate backgrounds with violet-indigo accent gradients
- **Glassmorphism**: `backdrop-blur`, semi-transparent card backgrounds
- **Custom keyframe animations**: `fadeInUp`, `slideIn`, `shimmer`, `pulse-glow`
- **Custom utility classes**: `.glass-card`, `.gradient-text`, `.hover-lift`, `.priority-badge`

#### [MODIFY] `layout.tsx`

- Add Google Fonts (Inter) via `next/font/google`
- Update `<title>` to "Shegxy Tasks"
- Add proper meta description
- Add dark class to `<html>` element
- Set proper `lang="en"` attribute

---

### 3. Branding & Navigation

#### [MODIFY] `Header.tsx`

Premium glassmorphic header redesign:
- **Logo**: "Shegxy" with gradient text effect and a subtle icon (using Lucide)
- **Glassmorphic navbar**: Semi-transparent with backdrop blur, border glow
- **User section**: Display user email with avatar placeholder, animated sign-out button
- **Sticky header** with subtle shadow on scroll

---

### 4. Sign-In Page

#### [MODIFY] `sign-in/page.tsx`

Premium sign-in experience:
- Dark gradient background with animated floating shapes
- Glassmorphic card with the Shegxy branding
- Styled CTA button with gradient and hover animation
- Footer with branding

---

### 5. Main Dashboard Page

#### [MODIFY] `(root)/page.tsx`

Transform into a full dashboard layout:
- **Stats bar**: 4 glassmorphic stat cards (Total, Pending, Completed, Overdue) with icons and animated counters
- **Filter/Search bar**: Search input + filter chips (All, Pending, Completed) + priority filter + category filter
- **Task sections**: Pending and Completed sections with count badges
- **Premium empty states**: Illustrated empty states with encouraging messages
- Pass new filter/search functionality as client-side state

#### [NEW] `DashboardStats.tsx`

Client component displaying 4 animated stat cards:
- Total Tasks (with trend icon)
- Pending (amber accent)
- Completed (emerald accent)
- Overdue (rose accent — tasks past `dueDate`)

#### [NEW] `SearchFilter.tsx`

Client component with:
- Glassmorphic search input with search icon
- Status filter pills (All / Pending / Completed)
- Priority filter dropdown
- Category filter dropdown
- All filters work client-side on the already-fetched data

#### [NEW] `EmptyState.tsx`

Reusable empty state component with:
- Lucide icon
- Title and subtitle text
- Optional CTA button
- Fade-in animation

---

### 6. Todo Components

#### [MODIFY] `CreateTodoForm.tsx`

Premium form redesign:
- Glassmorphic expandable form card with smooth open/close animation
- New fields: **Priority selector** (visual buttons with color-coded icons), **Due Date** picker, **Category** dropdown/input
- Floating "+" FAB button instead of plain text button
- Better form validation UX with inline error styling

#### [MODIFY] `TodoItem.tsx`

Premium todo card redesign:
- Glassmorphic card with hover-lift animation
- **Priority indicator**: Left border color strip (green=low, amber=medium, orange=high, red=urgent)
- **Priority badge**: Colored pill badge
- **Due date display**: With overdue highlighting (red glow if past due)
- **Category tag**: Subtle rounded tag
- Custom animated checkbox (circle with checkmark animation)
- Smooth edit/delete transitions
- Action buttons with icon-only design (Lucide icons) + tooltips
- Staggered entrance animation when list loads

#### [NEW] `PriorityBadge.tsx`

Small reusable component for colored priority pill badges.

#### [NEW] `TodoList.tsx`

Client wrapper component that handles:
- Receiving all todos as props
- Client-side filtering (search, status, priority, category)
- Rendering filtered results with staggered animations
- Managing filter state

---

### 7. Schemas & Actions

#### [MODIFY] `schemas.ts`

Add validation for new fields:
- `priority` field (enum validation)
- `dueDate` field (optional date)
- `category` field (optional string)

#### [MODIFY] `actions.ts`

Update `createTodo` and `updateTodo` actions to handle the new fields (priority, dueDate, category).

---

## Summary of Files

| Action | File | Purpose |
|--------|------|---------|
| MODIFY | `prisma/schema.prisma` | Add priority, dueDate, category fields |
| MODIFY | `src/app/globals.css` | Premium dark theme, glassmorphism, animations |
| MODIFY | `src/app/layout.tsx` | Inter font, Shegxy branding, dark mode |
| MODIFY | `src/components/Header.tsx` | Glassmorphic branded header |
| MODIFY | `src/app/sign-in/page.tsx` | Premium sign-in page |
| MODIFY | `src/app/(root)/page.tsx` | Dashboard layout with stats |
| MODIFY | `src/app/(root)/schemas.ts` | New field validations |
| MODIFY | `src/app/(root)/actions.ts` | Handle new fields |
| MODIFY | `src/app/(root)/components/CreateTodoForm.tsx` | Premium form with new fields |
| MODIFY | `src/app/(root)/components/TodoItem.tsx` | Premium card design |
| NEW | `src/app/(root)/components/DashboardStats.tsx` | Stats cards |
| NEW | `src/app/(root)/components/SearchFilter.tsx` | Search & filter bar |
| NEW | `src/app/(root)/components/EmptyState.tsx` | Empty state component |
| NEW | `src/app/(root)/components/PriorityBadge.tsx` | Priority badge component |
| NEW | `src/app/(root)/components/TodoList.tsx` | Client-side filter wrapper |

---

## Verification Plan

### Automated Checks
1. Run `npx prisma validate` to verify schema is valid
2. Run `npx prisma generate` to regenerate the Prisma client and Zod types
3. Run `npm run build` (with `SKIP_TS_BUILD=true` if no DB) to check for compilation errors
4. Run `npm run lint` to catch any linting issues

### Visual Verification
1. Launch `npm run dev` and open in browser to verify the redesigned UI
2. Check responsive design at mobile, tablet, and desktop breakpoints
3. Verify all animations and hover effects are smooth
4. Test the create/edit/delete flows with the new fields
