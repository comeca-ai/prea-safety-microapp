# Preá Safety Training Micro-app - Project Requirements Document (PRD)

## 1. Project Overview

This project is a mobile-first micro-application that lets kitesurfing students and instructors at Praia (Preá) beach complete a quick safety quiz and a pre-session checklist on their phones. By scanning a QR code on a beach sign, users are guided through an email-based Magic Link login and immediately presented with a short, multiple-choice quiz followed by a six-item readiness checklist. Each interaction is recorded so users can track their progress over time, and instructors can monitor overall safety engagement.

We’re building this to standardize safety training, reduce on-the-spot instruction time, and ensure every user reviews essential protocols before entering the water. Key success criteria include: 1) at least 80% of daily users complete both the quiz and checklist, 2) average quiz scores improve by 10% over successive attempts, and 3) average page load times under 2 seconds on 4G mobile connections.

## 2. In-Scope vs. Out-of-Scope

**In-Scope (MVP)**
- Email-based Magic Link authentication via Supabase
- Safety Quiz module (multiple-choice questions, immediate correctness feedback)
- AI-generated explanations for wrong answers via GPT API
- Pre-session Checklist (6 toggle items, simple save state)
- User History page showing past quiz attempts, scores, and checklist logs
- Mobile-first responsive UI with light/dark themes using shadcn/ui + Tailwind CSS
- Backend workflows triggered via n8n webhooks (e.g., sending post-quiz notifications)
- Docker and `docker-compose.yaml` for consistent local dev environment

**Out-of-Scope (Later Phases)**
- Admin interface for quiz question management
- Multi-language support
- Gamification features (badges, leaderboards)
- Offline mode or PWA caching
- Deep analytics dashboard (beyond simple history table)
- Video or multimedia content integration

## 3. User Flow

A new user arrives on the beach, scans the QR code, and the app opens in their browser. They land on the home page and click “Get Started,” which prompts them to enter their email. After submitting, they receive a Magic Link in their inbox—tapping that link logs them in without a password. Once logged in, they’re redirected to the main dashboard.

On the dashboard, the user first sees the “Safety Quiz” section. They answer each multiple-choice question; the app immediately indicates correct or incorrect answers. If an answer is wrong, a brief GPT-generated explanation appears. After finishing the quiz, the user moves on to the “Pre-session Checklist,” toggles each of the six items off or on, and submits. Finally, they see a confirmation screen and can tap “View History” to see their past attempts and scores in a simple data table.

## 4. Core Features

- **Authentication**: Email Magic Link sign-in powered by Supabase Auth SDK
- **Safety Quiz**: Fetch questions from Supabase (`quiz_questions`), record attempts (`quiz_attempts`), immediate correctness feedback
- **AI Explanations**: Call OpenAI GPT endpoint on wrong answers; display concise explanations
- **Pre-session Checklist**: Six toggle items saved as `checklist_sessions` in Supabase
- **User History**: Interactive data table showing past quiz scores, checklist completion dates
- **n8n Workflows**: Trigger webhooks for automation (e.g., send WhatsApp/email summaries)
- **Theming & UI**: Light/dark mode, large tappable controls, built with shadcn/ui + Tailwind CSS
- **Containerization**: Docker setup for local development mirroring production stack

## 5. Tech Stack & Tools

**Frontend:** Next.js (App Router), React, TypeScript, shadcn/ui components, Tailwind CSS v4

**Backend:** Supabase (Auth, PostgreSQL, RLS), Next.js API Routes / Server Actions

**AI & Automation:**
- OpenAI GPT-4 API (feedback explanations)
- n8n for external workflow automation (CSV import, notifications)

**DevOps & Tools:** Docker, docker-compose, GitHub Actions (future CI), ESLint & Prettier

## 6. Non-Functional Requirements

- **Performance:** Initial load < 2s on 4G mobile; quiz and checklist interactions < 0.5s response
- **Security:** All endpoints require logged-in session; Supabase Row Level Security ensures users see only their data; HTTPS only
- **Usability:** WCAG AA contrast for bright sunlight; tap targets ≥ 44×44px; mobile-first layout
- **Scalability:** Designed to handle hundreds of daily users with moderate read/write volume
- **Reliability:** 99.9% uptime expectation for Supabase and Next.js frontend

## 7. Constraints & Assumptions

- Supabase and OpenAI services are available with required API quotas
- Users have basic internet connectivity (no full offline support)
- Email addresses suffice for identity (no social login)
- n8n server or cloud instance is pre-configured for webhooks
- No specialized hardware; standard modern smartphones assumed

## 8. Known Issues & Potential Pitfalls

- **API Rate Limits:** Supabase free tier and OpenAI quotas may throttle if usage spikes.  Mitigation: cache GPT responses for repeated wrong answers; monitor usage.
- **Network Reliability:** Beach 4G can be spotty.  Mitigation: light UI payloads, retry logic, show clear offline/error messages.
- **Row Level Security (RLS) Complexity:** Misconfigured policies may block legitimate reads/writes.  Mitigation: write end-to-end tests for data access scenarios.
- **Email Delivery Delays:** Magic Link emails could be delayed or land in spam.  Mitigation: display clear “resend link” option and friendly error messages.
- **Accessibility in Bright Light:** Low contrast or small text could hamper readability.  Mitigation: test on actual beach devices; adjust color variables and font sizes.

---

This PRD lays out all requirements, flows, and technical decisions needed to build the Preá Safety Training Micro-app.  Subsequent documents (Tech Stack Document, Frontend Guidelines, Backend Structure, App Flow, File Structure, IDE rules) can now be drafted directly from this reference without ambiguity.