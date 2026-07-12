# EcoSphere

A corporate ESG (Environmental, Social & Governance) gamification platform that lets employees earn XP for sustainable actions and lets managers review, approve, and moderate submissions.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Mobile: Expo (React Native) with expo-router
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `artifacts/mobile/` — Expo mobile app (EcoSphere)
  - `app/(tabs)/index.tsx` — Root entry point (view-switched from context)
  - `context/AppContext.tsx` — All app state: users, orgStats, pendingReviews, actions
  - `components/AuthScreen.tsx` — Landing page + role selection bottom sheet
  - `components/EmployeePortal.tsx` — Employee portal (Home, Contribute, Activity tabs)
  - `components/ManagerPortal.tsx` — Manager portal (Workforce, Review tabs)
  - `components/FloatingParticles.tsx` — Animated background particles
  - `constants/colors.ts` — Dark nature-tech color palette
- `artifacts/api-server/` — Express API server
- `lib/api-spec/openapi.yaml` — OpenAPI contract (source of truth)

## Architecture decisions

- All app state is frontend-only in React context (no backend needed for first build) — AsyncStorage can be wired up for persistence later.
- View routing (`auth` | `employee` | `manager`) is handled via a single context state, not expo-router navigation, keeping the auth flow clean.
- Employee Portal and Manager Portal are self-contained components with their own internal tab state, making each portal independently testable.
- 20 mock users are seeded in context with diverse XP, streaks, statuses, and departments.
- Manager actions (approve/reject/flag/ban) update the shared users array in real time, affecting the leaderboard and all displays immediately.

## Product

- **Auth Portal:** Landing screen with org-wide impact stats (trees, watts, CO₂) + role-based login (Employee / Manager)
- **Employee Portal:**
  - Profile header with XP progress bar and streak counter
  - Top 10 leaderboard (filtered, excludes Banned users, gold/silver/bronze medals)
  - Evidence upload dropzone with simulated thumbnail preview
  - Activity log + achievement badges grid
- **Manager Portal:**
  - Workforce directory of all 20 employees (XP, status, suspect count)
  - Review queue with APPROVE / REJECT / FLAG (−15% XP) / BAN actions
  - Real-time state updates across all views

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- `useNativeDriver` animations fall back to JS on web — expected, not a bug on native devices.
- Floating particles use Animated API (no react-native-reanimated) for Expo Go compatibility.
- The `(tabs)/_layout.tsx` was repurposed to a plain Stack — no expo-router tab bar is used; custom tab bars are implemented in each portal component.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
