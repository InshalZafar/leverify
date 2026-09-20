# Leverify Quest — AI Course

This repository documents the full body of work completed throughout the
**Leverify Quest AI Course** — progressing from building and shipping a
complete full-stack web application to designing AI-driven automation
pipelines with Make.com and n8n, culminating in a final capstone project.

---

## Table of Contents

1. [Course Overview](#course-overview)
2. [Part 1 — Full-Stack Application: FixIt Now](#part-1--full-stack-application-fixit-now)
3. [Part 2 — Automation with Make.com](#part-2--automation-with-makecom)
4. [Part 3 — AI Agent Workflows with n8n](#part-3--ai-agent-workflows-with-n8n)
5. [Final Capstone — MealPlan](#final-capstone--mealplan)
6. [Skills Demonstrated](#skills-demonstrated)
7. [Links](#links)

---

## Course Overview

The course was structured in three progressive stages:

1. **Building a real, production-grade full-stack app** from scratch using
   Lovable and Supabase — covering authentication, database design, Row
   Level Security, live CRUD, and public deployment.
2. **Automating real-world workflows** without writing a full backend, using
   Make.com to connect Google Sheets, Google Slides, Gmail, and third-party
   APIs (Apify) into working pipelines.
3. **Building AI agent-driven workflows** with n8n — including a
   conversational chatbot, a scheduled AI analysis pipeline, and a
   multi-agent router system that delegates work to specialist agents.

The course concluded with an independent **final capstone project**: a
second full-stack application (MealPlan) built end-to-end without a
low-code platform, to demonstrate the underlying development skills learned
throughout.

---

## Part 1 — Full-Stack Application: FixIt Now

**FixIt Now** is a maintenance request and tracking app for residential
apartment complexes, connecting tenants, maintenance staff, and property
managers. Built with **Lovable + Supabase**, shipped to production on
**Netlify**.

### Assignment 1 — Planning & SDLC
- Defined the problem: tenants have no reliable way to report maintenance
  issues or track their status; staff have no central worklist; managers
  have no visibility or reporting.
- Defined three user roles — **Tenant**, **Maintenance Staff**, and
  **Property Manager (Admin)** — with a full permissions matrix (view,
  create, edit, approve, delete) for each.
- Documented business requirements, user requirements, and core features
  before any code was written.

### Assignment 2 — GitHub, Supabase & Authentication
- Connected the project to GitHub with full commit history.
- Set up a typed Supabase client, reading credentials from environment
  variables (never hardcoded).
- Created a `profiles` table with a database trigger that automatically
  inserts a profile row whenever a new user signs up.
- Enabled **Row Level Security** on `profiles`, restricting access to each
  user's own row.
- Built the landing page, sign-up, sign-in, and sign-out flows using
  Supabase Auth (`signUp`, `signInWithPassword`, `signOut`).
- Added route protection so signed-out users are redirected away from
  internal routes, and signed-in users are redirected away from
  sign-in/sign-up.
- Synchronized auth state app-wide via Supabase's `getSession()` and
  `onAuthStateChange()` — no `localStorage`/`sessionStorage` used for auth.

### Assignment 3 — Database & Live Data Flow
- Designed a full relational schema: `buildings`, `units`, `user_roles`,
  `categories`, `requests`, `comments`, `notifications` — with foreign
  keys, unique constraints, an auto-updating `updated_at` trigger, and
  seed data.
- Wrote Row Level Security policies for every table, including a
  `SECURITY DEFINER` helper function (`get_user_role`) to avoid recursive
  RLS checks.
- Added role-based sign-up (tenant/staff, with admin assigned manually) and
  role-aware routing straight to the correct dashboard.
- Built full live data flows for each role:
  - **Tenant:** submit requests, view live status, comment, cancel pending
    requests.
  - **Staff:** live assigned worklist sorted by priority, status
    progression (Assigned → In Progress → Completed).
  - **Admin:** live dashboard metrics, full requests table with
    multi-filtering, staff assignment, verify & close, user/role
    management.
- Verified Row Level Security is enforced at the **database level**, not
  just in the UI — confirmed one tenant cannot see another tenant's data.

### Assignment 4 — Advanced UI & Interactive Insights
- Conducted a usability audit before adding features, identifying real gaps
  (no filter chips, no pagination, plain HTML bars instead of charts).
- Implemented debounced search, multi-field filters, sorting, exact result
  counts, active filter chips, a Reset Filters button, and pagination — all
  via parameterized Supabase queries (not client-side array filtering).
- Replaced plain HTML progress bars with **Recharts** bar and area charts
  (requests per category, 30-day submission/resolution trend), with
  accessibility labels (`role="img"`, `aria-label`).
- Added role-aware breadcrumbs and fixed navigation dead ends.
- Added skeleton loading states for KPI tiles and charts.

### Assignment 5 — Production Deployment & Public Launch
- Deployed to **Netlify**, connected via GitHub for continuous deployment
  (every push to `main` triggers a new build).
- Added a `_redirects` file so nested app routes load correctly on direct
  visit and refresh (no 404s on a single-page app).
- Configured environment variables directly in Netlify (never committed to
  GitHub) — only the browser-safe Supabase anon key is used in the
  frontend.
- **Issue found and fixed during deployment:** the `service_role` (secret)
  key was mistakenly set instead of the `anon` key. Supabase correctly
  blocked this at runtime; the correct key was set, the site rebuilt with a
  cache clear, and the exposed key was rotated as a precaution.
- Verified the full production app in an incognito window: auth, role-based
  routing, full CRUD flow, charts, and responsive layout all confirmed
  working against the live Supabase project.

**Live App:** https://fixitnow-inshal.netlify.app
**Repository:** https://github.com/InshalZafar/fixit-skeleton

---

## Part 2 — Automation with Make.com

### Assignment 6 — Automated Certificate Generator
A Make.com scenario that reads a student's name, date, and email from a
Google Sheet, generates a personalized certificate from a Google Slides
template (replacing `{{Name}}` and `{{Date}}` tags), exports it as a PDF via
Google Drive, and emails it automatically — with no manual editing or
sending.

- **Flow:** Google Sheets (watch new rows) → Google Slides (create from
  template) → Google Drive (export as PDF) → Gmail (send email)
- **Issues fixed during build:** a 404 from using a sheet name instead of
  its Spreadsheet ID; a Gmail attachment validation failure fixed by using
  the built-in Drive-download attachment option instead of a raw file map;
  a File ID field defaulting to a static file instead of the dynamically
  created certificate.
- Tested with three rows end-to-end; all modules completed successfully.

### Assignment 7 — Supplier Research Automation
An end-to-end Make.com pipeline that reads brand names from a Google Sheet,
searches for each brand via an **Apify** Google Search actor, extracts
contact details from the results using a second Apify actor, and writes the
brand name, source URL, and contact details back to an output sheet.

- **Flow (6 modules):** Google Sheets (search rows) → Apify (run Google
  Search actor) → Apify (get dataset items) → Apify (run Contact Details
  actor) → Apify (get dataset items) → Google Sheets (add row)
- Required setting up separate input/output Google Sheets, an Apify account
  with two activated actors, and connections for both Google Sheets and
  Apify inside Make.com.

---

## Part 3 — AI Agent Workflows with n8n

### Assignment 10 — Automated Article Summarization
An n8n workflow that runs on a **daily schedule**, reads pending article
links from a Google Sheet, sends each article's cleaned content to a
**Gemini AI Agent** for critical analysis, and writes the structured result
back into the correct row.

- **Flow:** Schedule Trigger → Google Sheets (read) → Filter (pending rows
  only) → Loop Over Items → HTTP Request (fetch article HTML) → Code (clean
  text) → AI Agent (Gemini) → Code (parse JSON) → Google Sheets (update
  row)
- The AI Agent is instructed to separate facts from the author's opinion,
  never invent information, and return a clear error rather than guess when
  content is unavailable.
- Output columns: Title, Summary, Pros, Cons, Long-Term View, Author
  Verdict, Status, Processed At.
- A Filter node prevents already-completed rows from being reprocessed on
  subsequent runs.

### Assignment 10 (extended) — Pexels Image Download Chatbot
A conversational n8n automation where a user requests an image in natural
language via chat.

- **Flow:** Chat Trigger → AI Agent (with a Pexels HTTP search tool
  attached) → Parse/Validate → Download Image (binary) → Google Drive
  Upload → Chat Response
- The AI Agent turns a natural-language request (e.g. "a professional
  office team image") into a concise Pexels search query, selects a
  relevant photo, and confirms the filename, Drive destination,
  photographer, and source page back to the user after upload.
- API credentials (Pexels key, Google Drive OAuth) are stored securely as
  n8n credentials — never placed in visible node fields or prompts.

### Assignment 12 — Organizational Multi-AI Agent Assistant
An n8n assistant that separates **routing from execution**: a Router AI
Agent inspects each incoming request and selects exactly one specialist —
**Planner**, **Note Taker**, or **Task Refiner** — while the other two
branches remain completely inactive for that run.

- **Architecture:** Chat Trigger (text/PDF) → Input Preparation → Router AI
  Agent → Switch → 3 Specialist Agents → Google Sheets / Chat Response
- **Routing method:** a dedicated AI Agent classifies the message into
  exactly one of four labels (`Planner`, `NoteTaker`, `TaskRefiner`, or
  `Clarify` for unclear intent); a Switch node then activates only the
  matching branch using exact-string comparison.
- This design avoids the unpredictability of one large agent guessing what
  to do — the router's only job is classification, and it is explicitly
  instructed not to answer the user's request itself.
- Backed by two Google Sheets (Tasks & Goals, Outputs) for persistent state
  across specialist agents.

---

## Final Capstone — MealPlan

The course concluded with an independently built full-stack application —
**MealPlan**, a recipe manager and weekly meal planner — built without a
low-code platform, using a traditional Node.js/Express backend, to
demonstrate the underlying development skills learned throughout the
course.

### What it does
- Users save recipes with ingredients and instructions.
- Users assign recipes to a weekly planner grid (day × meal type).
- The app automatically generates a single, merged shopping list —
  ingredients shared across multiple planned recipes (e.g. garlic used in
  two different recipes) are normalized and summed into one line rather
  than listed twice.

### How it was built
- **Stack:** Node.js, Express, EJS (server-rendered views), SQLite
  (via Node's built-in `node:sqlite` module — no native compilation
  required)
- **Auth:** bcrypt-hashed passwords, session-based authentication,
  per-user data isolation enforced at the query level
- **Core logic:** a merge algorithm that counts recipe usage across the
  week, normalizes ingredient names, and sums quantities grouped by
  `(name, unit)` inside a database transaction
- **Testing:** manually verified the full user journey, the ingredient
  merge logic (confirmed correct with a live example: 2 + 3 = 5 pieces of
  garlic across two recipes), auth/route protection, and responsive layout

This project mirrors the same principles applied throughout the course —
clear requirements before code, a properly normalized database, enforced
data isolation, and a genuinely tested core feature — just implemented with
a hand-written backend instead of a low-code platform.

---

## Skills Demonstrated

| Category | Skills |
|---|---|
| **Full-stack development** | React (via Lovable), Node.js/Express, EJS, REST-style routing, CRUD design |
| **Databases** | Supabase (PostgreSQL), SQLite, schema design, foreign keys, constraints, triggers |
| **Security** | Row Level Security, password hashing, session-based auth, environment-variable secrets management, credential rotation after exposure |
| **Automation** | Make.com scenario building, module chaining, error handling and debugging live automations |
| **AI Agents** | Prompt/system-message design, AI-based routing and classification, structured JSON output parsing, multi-agent architecture (n8n) |
| **Data visualization** | Recharts (bar/area charts), KPI dashboards |
| **Deployment & DevOps** | GitHub version control, CI/CD via Netlify, environment configuration, production verification and incident response |
| **Planning & documentation** | SDLC-style planning documents, user role definitions, requirements gathering, testing documentation |

---

## Links

| Project | Link |
|---|---|
| FixIt Now (Live App) | https://fixitnow-inshal.netlify.app |
| FixIt Now (Repository) | https://github.com/InshalZafar/fixit-skeleton |
| MealPlan Capstone (Repository) | https://github.com/InshalZafar/capstone |

---

*All projects and automations documented above were built as part of the
Leverify Quest AI Course.*
