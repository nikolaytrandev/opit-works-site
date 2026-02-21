# Project

[App name] — [one sentence description of what it does and who it's for]

## Stack

- **Framework:** Next.js (App Router) + TypeScript
- **Styling:** Tailwind CSS + Tailwind UI components
- **Auth:** Clerk
- **Database:** PostgreSQL via Prisma
- **Icons:** Heroicons

---

## Local development

Dev server runs on **http://localhost:3000** — always use this for screenshots and browser testing.

```bash
npm run dev        # start dev server
npm run build      # production build
npm run typecheck  # run tsc without emitting
npm run lint       # eslint
```

Database:
```bash
npx prisma studio          # open DB browser at localhost:5555
npx prisma db push         # push schema changes (dev only)
npx prisma generate        # regenerate client after schema changes
```

Before running the app for the first time:
```bash
npm install
npx prisma generate
npx prisma db push
```

---

## Project structure

```
src/
  app/              # Next.js App Router pages and layouts
  components/
    ui/             # Shared reusable components (Button, Input, Card, etc.)
    features/       # Feature-specific components (not reusable across features)
  lib/              # Utilities, helpers, constants
  hooks/            # Custom React hooks
  types/            # Shared TypeScript types
  server/           # Server actions and API logic
prisma/
  schema.prisma
```

---

## Component library

**Always use `src/components/ui/` for shared UI elements.**

Before writing any UI, check if a component already exists there. If it does, use it. If it doesn't and the pattern will be used more than once, create it.

Never write one-off inline Tailwind for things that should be reusable (buttons, inputs, cards, badges, avatars). Inline styles are fine for one-time layout adjustments.

When creating a new component:
- Add it to `src/components/ui/`
- Export it from `src/components/ui/index.ts`
- Support `className` prop for overrides via `cn()` utility
- Type all props explicitly — no implicit `any`

---

## Mobile-first always

This is a **mobile-first consumer app**. Every UI decision starts at 390px and scales up.

- Write Tailwind classes mobile-first: `text-sm md:text-base`, never the reverse
- Minimum touch target: 44×44px on all interactive elements
- Test layouts at 390px before considering larger breakpoints
- Prefer bottom navigation over sidebars on mobile

---

## Code conventions

**TypeScript**
- Strict mode is on — no `any`, no `ts-ignore` without a comment explaining why
- Prefer `type` over `interface` for object shapes
- All server actions and API functions must have explicit return types

**React**
- Functional components only
- Colocate state as close to where it's used as possible
- Avoid prop drilling more than 2 levels — use context or lift to a shared parent
- Prefer server components by default; add `'use client'` only when needed (event handlers, hooks, browser APIs)

**Naming**
- Components: `PascalCase`
- Hooks: `useCamelCase`
- Utilities: `camelCase`
- Constants: `SCREAMING_SNAKE_CASE`
- Files: match the primary export name (`UserCard.tsx`, `useAuth.ts`)

**Imports**
- Use `@/` path alias for all internal imports
- Group imports: external libraries → internal modules → relative imports
- No barrel imports from large libraries (`import { X } from 'lodash'` not `import _ from 'lodash'`)

---

## Data fetching

- Fetch data in Server Components where possible — avoid unnecessary client-side fetching
- Use server actions for mutations (`'use server'`)
- Always handle loading, error, and empty states — never assume data exists
- Optimistic updates for actions the user will do frequently (likes, toggles, reorders)

---

## Error handling

- Never swallow errors silently — log or surface them
- User-facing error messages should be human-readable, never raw error strings
- Form validation errors go inline next to the field, not in a toast
- Network/server errors go in a toast or banner, not inline
- Always have a fallback UI for failed data fetches

---

## Visual verification

After making any UI change, take a screenshot to verify it looks correct before moving on:

```bash
python3 ~/.claude/skills/screenshot-dev/scripts/screenshot.py http://localhost:3000 /tmp/screenshot.png
python3 ~/.claude/skills/screenshot-dev/scripts/screenshot.py http://localhost:3000 /tmp/mobile.png --device "iPhone 14"
```

Don't declare a UI task done without visually confirming it. "It should look right" is never sufficient.

---

## What not to do

- Don't install new dependencies without checking if something already in the stack can do it
- Don't use `useEffect` for data fetching — use server components or SWR/React Query
- Don't hardcode colors or spacing outside of Tailwind classes
- Don't commit `.env` files or API keys
- Don't write tests for UI components unless explicitly asked — focus on business logic
- Don't add `console.log` statements to committed code
- Don't create new files speculatively — only create what's needed for the current task

---

## When you're unsure

- Check existing components in `src/components/ui/` before creating something new
- Check existing patterns in nearby files before inventing a new approach
- Ask before adding a new dependency
- Ask before changing the database schema