---
tags: [project, moc, demo]
status: active
created: 2026-04-13
---

# Demo Web App — Project Index

> **Đây là demo project minh họa cách vault tự động ghi nhận context cho Claude Code.**
>
> Khi bạn `cd` vào một project có tên `demo-web-app`, Claude sẽ đọc file này đầu session để hiểu ngữ cảnh.

## Overview

Example web application built with Next.js + TypeScript + Tailwind.

## Status

- **Phase:** Development
- **Priority:** Medium
- **Last Active:** 2026-04-13

## Tech Stack

- **Framework:** Next.js 15 (App Router)
- **Language:** TypeScript
- **Styling:** Tailwind CSS
- **Database:** PostgreSQL + Drizzle ORM
- **Auth:** Better Auth
- **Deployment:** Vercel

## Key Links

- **Repo:** https://github.com/example/demo-web-app
- **Decisions:** [[memory/decisions-log]]
- **Related Wiki:** [[wiki/technologies/nextjs]], [[wiki/patterns/error-handling]]

## Architecture

```
src/
├── app/              # Next.js App Router
├── components/       # React components
├── lib/              # Utilities
└── db/               # Database schema & queries
```

## Key Files

- `src/app/layout.tsx` — Root layout
- `src/lib/auth.ts` — Auth config
- `drizzle.config.ts` — Database config

## Active Focus

- Implement user profile page
- Add OAuth providers (Google, GitHub)
- Migrate from pages to app router
