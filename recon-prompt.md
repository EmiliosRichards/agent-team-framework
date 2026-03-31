# Repo Reconnaissance Prompt

Hand this to an agent with access to the target repo. Copy everything below the line.

---

I need you to produce a comprehensive reconnaissance report on this codebase. This will be used to configure an AI agent team for the project. Be thorough and specific — generic descriptions are useless.

## 1. Project Overview
- What does this app/service do? Who is it for?
- Is this a monorepo? If so, list every app/service/package with a one-line description of each.
- Which specific app or service am I asking you to focus on? (If unclear, list them and ask me.)

## 2. Tech Stack
For the focused app/service:
- Language(s) and version(s)
- Framework(s) and version(s) (e.g., Next.js 14 with App Router, React 19, etc.)
- Styling approach (Tailwind, CSS modules, styled-components, etc.)
- State management (Redux, Zustand, Context, server components, etc.)
- Database and ORM (PostgreSQL + Prisma, MongoDB + Mongoose, etc.)
- Authentication (NextAuth, Clerk, custom, etc.)
- API style (REST, tRPC, GraphQL, server actions, etc.)
- Deployment (Vercel, Railway, Docker, etc.)
- Package manager (npm, pnpm, yarn, bun)
- Monorepo tool if applicable (Turborepo, Nx, etc.)

## 3. Directory Structure
Show the full directory tree (2-3 levels deep) for the focused app with annotations:
```
app/
├── (dashboard)/     ← main user-facing pages
│   ├── page.tsx     ← dashboard home
│   └── settings/    ← user settings
├── api/             ← API routes
│   ├── auth/        ← authentication endpoints
│   └── ...
├── components/      ← shared UI components
...
```
Note which directories are large/active vs small/stale.

## 4. Key Files
List the 10-15 most important files and what they do:
- Main layout/entry points
- Configuration files (next.config, tsconfig, tailwind.config, etc.)
- Database schema
- Environment variable usage (.env.example or equivalent)
- Key shared utilities/hooks

## 5. Current State Assessment
- How many pages/routes does the app have?
- Rough line count / file count for the focused app
- Are there tests? What kind? What coverage roughly?
- Is there a CI/CD pipeline? What does it do?
- Any obvious technical debt? (TODO comments, deprecated patterns, inconsistent conventions)
- Are there existing docs? (README, CONTRIBUTING, etc.)

## 6. UI/Styling Patterns
Since the first work will be UI improvements:
- What component library is used? (shadcn/ui, MUI, Chakra, custom, etc.)
- Is there a design system or consistent styling approach?
- Are there reusable UI components? Where do they live?
- Is the styling consistent across pages or does it vary?
- Are there accessibility considerations in place?
- Is the app responsive? How is responsive design handled?
- Dark mode support?
- What does the color palette / theme setup look like?

## 7. Pain Points (Your Observations)
As you explore the codebase, note:
- Inconsistencies in code patterns (some pages use one approach, others use a different one)
- Duplicated code that could be consolidated
- Performance concerns (large bundles, unnecessary re-renders, no lazy loading)
- Styling inconsistencies (different spacing, colors, font sizes across pages)
- Missing error handling or loading states
- Anything that looks fragile or likely to break

## 8. User Pain Points (ask the user these — don't guess)

Before writing your report, ask the user:
1. What frustrates you most about working on this codebase?
2. What breaks most often?
3. What do users/customers complain about?
4. Is there anything you've been wanting to fix but haven't had time for?
5. Are there any areas of the code you're afraid to touch?

Include their answers verbatim in the report — this context is often more valuable than automated code analysis.

## 9. Monorepo Context (if applicable)
- What shared packages exist? Which ones does the focused app import?
- Are there shared UI components across apps?
- Is there a shared database or do apps have separate databases?
- What's the build/dev workflow? (Turbo commands, workspace scripts, etc.)
- Are there any cross-app dependencies that would affect changes to the focused app?

## Output Format

Write the report as a markdown file. Be specific — include actual file paths, actual component names, actual config values. "The app uses React" is useless. "Next.js 14.2.3 with App Router, React 19, TypeScript 5.4, Tailwind CSS 3.4 with shadcn/ui components in `components/ui/`, Prisma 5.x with PostgreSQL, deployed on Vercel" is useful.

Save the report as `recon-report.md` in the project root (or wherever makes sense).
