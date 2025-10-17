# Preá Safety Micro-app Backend Structure Document

This document outlines the backend setup for the Preá Safety Training micro-application. It describes each component in everyday language so that anyone—from newcomers to non-technical stakeholders—can understand how the system works, how it’s hosted, and how it stays secure and reliable.

## 1. Backend Architecture

### Overall Design
- Built on **Next.js** using the App Router. This gives us:
  - **Serverless functions** (API routes) for backend logic without managing our own servers.
  - **Server Components** for fast initial loads and **Client Components** for interactive features (the quiz and checklist).
- Written in **TypeScript** to catch errors early and keep code maintainable.
- Organized in layers:
  1. **API Layer** (Next.js routes) handles incoming requests.
  2. **Service Layer** contains business logic (quiz scoring, checklist state).
  3. **Data Access Layer** uses the Supabase client to read/write data.

### Scalability, Maintainability, Performance
- **Scalability**
  - Serverless backend on Vercel scales automatically with traffic.
  - Supabase (PostgreSQL) managed by Supabase’s cloud ensures the database can grow.
- **Maintainability**
  - Modular code (separate files for auth, data, and UI components).
  - TypeScript types generated from the database schema to keep frontend and backend in sync.
- **Performance**
  - Server Components minimize JavaScript sent to the client.
  - Vercel’s global CDN delivers pages and static assets close to users.
  - Edge caching for API responses where appropriate (e.g., quiz questions).

## 2. Database Management

### Technologies Used
- **Type**: Relational (SQL)
- **Provider**: Supabase (managed PostgreSQL)

### Data Storage and Access
- Tables are defined in Supabase and follow a clear naming convention:
  - `quiz_questions` stores each quiz question and possible answers.
  - `quiz_attempts` logs every user’s quiz submission and score.
  - `checklist_sessions` records each pre-session checklist completion.
- **Row-Level Security (RLS)** is enabled so users only see their own records.
- The Next.js backend uses the `@supabase/auth-helpers-nextjs` SDK and `@supabase/supabase-js` to connect securely.
- Environment variables store keys and URLs, following 12-factor app guidelines.

## 3. Database Schema

### Human-Readable Overview
- **quiz_questions**
  - `id`: unique identifier for each question
  - `question_text`: the question itself
  - `options`: list of answer choices
  - `correct_option`: index or key pointing to the right answer
- **quiz_attempts**
  - `id`: unique identifier for each attempt
  - `user_id`: which user took the quiz
  - `question_id`: which question was answered
  - `selected_option`: what the user chose
  - `is_correct`: true/false
  - `attempted_at`: timestamp of the submission
- **checklist_sessions**
  - `id`: unique identifier for each session
  - `user_id`: who completed the checklist
  - `item_statuses`: list of booleans for each checklist item
  - `completed_at`: timestamp when checklist was finished

### SQL Schema (PostgreSQL)
```sql
-- Quiz Questions Table
define table quiz_questions (
  id             uuid       primary key,
  question_text  text       not null,
  options        jsonb      not null,
  correct_option integer    not null,
  created_at     timestamptz default now()
);

-- Quiz Attempts Table
define table quiz_attempts (
  id              uuid       primary key,
  user_id         uuid       not null references auth.users(id),
  question_id     uuid       not null references quiz_questions(id),
  selected_option integer    not null,
  is_correct      boolean    not null,
  attempted_at    timestamptz default now()
);

-- Checklist Sessions Table
define table checklist_sessions (
  id             uuid       primary key,
  user_id        uuid       not null references auth.users(id),
  item_statuses  jsonb      not null,
  completed_at   timestamptz default now()
);
```

## 4. API Design and Endpoints

We follow **RESTful** conventions using Next.js API routes.

### Key Endpoints
- **Authentication**
  - `POST /api/auth/magic-link` — sends a login link to the user’s email.
- **Quiz**
  - `GET /api/quiz/questions` — fetches all quiz questions.
  - `POST /api/quiz/attempts` — records a user’s answers and returns feedback.
- **Checklist**
  - `GET /api/checklist` — retrieves the 6 checklist items.
  - `POST /api/checklist/sessions` — saves the completed checklist state.
- **History**
  - `GET /api/history/quiz` — returns past quiz attempts for the logged-in user.
  - `GET /api/history/checklist` — returns past checklist sessions.
- **Automation & AI**
  - `POST /api/webhooks/n8n` — triggers an n8n workflow (e.g., send WhatsApp feedback).
  - `POST /api/ai/generate-explanation` — calls OpenAI’s GPT API to generate a short explanation for wrong answers.

All endpoints require a valid Supabase JWT token in the Authorization header.

## 5. Hosting Solutions

- **Frontend & API**: Hosted on **Vercel**
  - Pros: automatic deployment from Git, global CDN, zero-config serverless functions.
- **Database & Auth**: Managed by **Supabase**
  - Pros: auto-scaling Postgres, built-in authentication, easy RLS setup, daily backups.
- **n8n Workflows**: Can run on **n8n Cloud** or a small Docker VM
  - Pros: visual workflow builder, webhook receivers, easy integration with Supabase and OpenAI.

This combination balances reliability, quick deployments, and predictable costs.

## 6. Infrastructure Components

- **Load Balancer & CDN**
  - Handled by Vercel: routes incoming traffic to the nearest edge server.
- **Caching**
  - Edge caching for static pages and quiz questions.
  - In-memory caches (SWR) on the client for recent API calls.
- **Containerization (Local Dev)**
  - **Docker** and **docker-compose** to spin up a local environment mirroring production: Next.js app, mock Supabase (or direct cloud), and n8n.
- **Content Delivery Network (CDN)**
  - Vercel’s built-in CDN for assets (images, CSS, JavaScript bundles).

These components work together to ensure fast load times, even at busy times on the beach.

## 7. Security Measures

- **Authentication**
  - Email-based **Magic Link** via Supabase Auth—no passwords to manage.
  - JSON Web Tokens (JWT) for securing API routes.
- **Authorization**
  - Row-Level Security (RLS) policies in Supabase to restrict data access.
  - Middleware in Next.js checks each request’s JWT before proceeding.
- **Data Encryption**
  - TLS/HTTPS enforced for all data in transit.
  - Supabase encrypts data at rest in the managed database.
- **Secrets Management**
  - Environment variables store all keys (Supabase, n8n, OpenAI) outside of code.
  - Rotated regularly according to security best practices.

## 8. Monitoring and Maintenance

- **Performance Monitoring**
  - Vercel Analytics to track latency, cold starts, and error rates.
  - Supabase Dashboard for database performance and slow query logs.
- **Error Tracking**
  - (Optional) Integrate Sentry or LogRocket for capturing client-side and server-side exceptions.
- **Workflows Monitoring**
  - n8n Cloud’s built-in logging for each webhook and workflow execution.
- **Maintenance Strategy**
  - Weekly dependency updates via Dependabot or Renovate.
  - Automated database backups handled by Supabase.
  - Quarterly reviews of RLS policies and environment variables.

## 9. Conclusion and Overall Backend Summary

The Preá Safety micro-app backend is built for reliability, speed, and ease of use. By leveraging serverless Next.js on Vercel and a managed PostgreSQL from Supabase, we achieve:

- A **scalable** and **cost-effective** hosting environment.
- A **secure** authentication and data storage system with minimal friction for users.
- A clear, **modular** code structure in TypeScript that’s easy to maintain and extend.

Automation via n8n and AI-powered feedback through GPT enrich the user experience without complicating the core architecture. Overall, this setup aligns perfectly with the project’s goals of fast loading times, straightforward safety training, and future growth—ensuring every kitesurf student can complete their pre-session checklist quickly and safely.