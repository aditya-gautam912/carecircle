# CareCircle

**Family care-coordination for aging parents.** CareCircle keeps the whole family in the loop — medications, daily check-ins, emergency alerts, and a shared feed — with live updates for every member of the care circle.

**Live demo:** https://eldercare-app-dusky.vercel.app

## Features

- **Dashboard** — create elder profiles; the creator automatically becomes the family admin
- **Elder detail page** with 5 tabs: Check-ins, Medications, Contacts, Invite, Feed
- **Medication tracking** — full CRUD with dosage, frequency, time, and notes
- **Daily check-ins** — one-tap "All good" / "Needs help", with paginated history
- **Emergency contacts** — add, inline-edit, and delete contacts
- **Invite system** — generate 8-char invite codes, copy shareable links, revoke codes, public `/invite/[code]` landing page
- **Family feed** — combined timeline of check-ins and medications with pagination
- **Emergency alert** — one-tap button that flags "needs help" for the whole family instantly
- **Supabase Realtime** — new check-ins and medications appear live for other family members, with toast notifications
- **Auth** — login/signup, password reset flow, invite-flow redirects, route-guarded middleware
- **Dark mode** — class-based toggle, persisted to localStorage, respects system preference
- **Responsive & mobile-first** — hamburger nav, staggered animations, loading skeletons, inline validation

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 16 (App Router), React, TypeScript |
| Styling | Tailwind CSS v4 (class-based dark mode) |
| Backend | Supabase — Auth, Postgres with Row-Level Security, Realtime |
| Testing | Vitest + React Testing Library (**73 tests, 8 files**) |
| Mobile | Capacitor 8 Android wrapper (`com.carecircle.app`) |
| Hosting | Vercel |

## Getting started

```bash
git clone https://github.com/aditya-gautam912/carecircle.git
cd carecircle
npm install
cp .env.local.example .env.local   # add your Supabase URL + anon key
npm run dev                        # http://localhost:3000 (use -p 3001 if 3000 is taken)
```

Then run `supabase/schema.sql` in the Supabase SQL Editor to create the tables and RLS policies.

```bash
npm test           # run the 73-test suite
npm run lint       # eslint
npm run build      # production build
```

### Environment variables

```bash
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
```

## Architecture notes

- **Security via RLS** — Row-Level Security policies restrict every table to an elder's family members; policies use `UNION`-style subqueries (`elders.created_by`) to avoid recursive-policy infinite loops.
- **Client-side auth pages** — auth routes are `"use client"` with `force-dynamic` to avoid SSR prerender failures.
- **Realtime subscriptions** — per-elder subscriptions to `check_ins` and `medications` INSERT events, filtered to skip the current user's own actions and idempotent against concurrent loads.
- **Pagination** — fetches `PAGE_SIZE + 1` rows to detect "Load more" honestly, then trims to `PAGE_SIZE`.

## Android build

```bash
npm run android:sync     # sync web assets into the Android project
cd android && ./gradlew assembleDebug
# → android/app/build/outputs/apk/debug/app-debug.apk
```

## Project structure

```
src/app/            # pages: landing, dashboard, elder detail, invite, auth flows
src/components/     # design system (Button, Card, Input, Toast, …), header, theme
src/lib/supabase/   # Supabase client/server/middleware factories
src/middleware.ts   # auth guard for all app routes
supabase/schema.sql # tables + RLS policies
android/            # Capacitor-wrapped native project
```
