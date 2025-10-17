# Tech Stack Document for Preá Safety Training Micro-app

This document explains, in everyday language, the main technologies and tools used in the Preá Safety Training Micro-app. Our goal is to show why each choice was made and how it contributes to a fast, secure, and user-friendly experience—without assuming any technical background.

## 1. Frontend Technologies
These are the building blocks that the user interacts with directly in their browser or mobile device:

- **Next.js (App Router)**  
  A modern framework that makes pages load quickly. It handles server-side rendering (SSR) and static site generation (SSG) so that content appears almost instantly—even on slower 4G connections at the beach.

- **TypeScript**  
  A version of JavaScript that catches mistakes early by checking types (for example, making sure you don’t mix up numbers and words). This helps keep the code reliable and easier to update.

- **shadcn/ui**  
  A library of ready-made user interface components (buttons, cards, switch toggles). It ensures a consistent, mobile-first look and feel, so everything—from quiz radio buttons to checklist toggles—behaves and looks the same.

- **Tailwind CSS v4 with CSS Variables**  
  A styling tool that lets us write simple, human-readable class names for colors, spacing, and layout. CSS variables make it easy to switch themes (light/dark mode) based on ambient lighting, like bright sunlight versus evening sessions.

- **Responsive Design Principles**  
  All pages and components are built with mobile-first layouts in mind—large tap targets, clear fonts, and adaptive spacing—so users can complete the quiz and checklist comfortably on their phones.

## 2. Backend Technologies
These tools handle data storage, user accounts, and server-side logic behind the scenes:

- **Supabase (Auth & Database)**  
  A managed service that provides both user authentication and a PostgreSQL database:
  • **Magic Link Authentication**: Users enter their email and receive a secure login link—no passwords to remember.  
  • **PostgreSQL Database**: Stores quiz questions, user attempts, and checklist sessions.  
  • **Row Level Security (RLS)**: Ensures each user only sees their own data.

- **supabase-js & @supabase/auth-helpers-nextjs**  
  Official Supabase libraries that make it easy to connect from Next.js pages and API routes.

- **Next.js API Routes & Server Actions**  
  Built-in server endpoints for handling form submissions, database queries, and secure calls to third-party services (like n8n or GPT) without exposing secrets to the browser.

## 3. Infrastructure and Deployment
How the app is hosted, updated, and kept running smoothly:

- **Version Control (Git & GitHub)**  
  All code is tracked and shared via Git on GitHub. This ensures a clear history of changes and easy collaboration.

- **Continuous Deployment (Vercel)**  
  Every time we push code to the main branch, Vercel automatically builds and deploys the updated micro-app. This means new features and fixes go live in minutes, without manual steps.

- **Supabase Cloud Hosting**  
  The database and authentication services are hosted by Supabase’s managed platform, ensuring high availability and automatic backups.

- **Docker & Docker Compose (Local Development)**  
  Developers can spin up the same environment on their laptops—complete with Next.js and any service mocks—using a simple `docker-compose up` command. This avoids the "it works on my machine" problem.

## 4. Third-Party Integrations
Additional services that extend the app’s capabilities:

- **n8n (Workflow Automation)**  
  An open-source automation tool that:
  • Imports quiz questions from a CSV file.  
  • Sends personalized feedback via email or WhatsApp after quiz completion.

- **OpenAI GPT API**  
  Generates short, standardized explanations for any incorrect quiz answers. We call it from a secure server route so API keys stay safe.

- **Environment Variables**  
  Keys and URLs for Supabase, OpenAI, and n8n are stored in a hidden `.env.local` file—never in the public code—to protect sensitive information.

## 5. Security and Performance Considerations
Measures we’ve taken to keep data safe and ensure a smooth experience:

- **Secure Authentication**  
  Magic links mean no passwords are stored or transmitted. Supabase’s RLS policies prevent users from seeing or changing other users’ data.

- **HTTPS Everywhere**  
  All communication between the user’s device, Vercel, and Supabase happens over encrypted HTTPS connections.

- **Minimal Client-Side JavaScript**  
  By leveraging Next.js server rendering for core pages, we reduce the amount of code the browser needs to download and run. This leads to faster page loads and lower data usage.

- **Error Handling & User Feedback**  
  All network requests include clear success and error messages, helping users understand what’s happening (e.g., “Quiz saved!” or “Network error, please try again”).

- **Accessibility**  
  We follow best practices—proper color contrast, large tap zones, and semantic HTML—to ensure the app is easy to use under bright sunlight and accessible to all.

## 6. Conclusion and Overall Tech Stack Summary
Our Preá Safety Training Micro-app combines modern, proven technologies to deliver a fast, reliable, and user-friendly experience:

- Frontend: Next.js, TypeScript, shadcn/ui, Tailwind CSS
- Backend: Supabase (Auth + PostgreSQL), Next.js API Routes
- Automation: n8n workflows, OpenAI GPT for feedback
- Infrastructure: GitHub + Vercel for code and deployment, Docker for local setup
- Security & Performance: Magic Link login, RLS, HTTPS, optimized rendering, accessibility practices

This combination ensures that students and instructors can quickly scan a QR code, log in with a single click, complete a safety quiz and checklist, and review their history—all within seconds, even on a slow connection at the beach. The choices we’ve made emphasize simplicity, security, and speed, aligning perfectly with the project’s goals and user needs.