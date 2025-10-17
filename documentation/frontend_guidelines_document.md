# Frontend Guidelines for Preá Safety Micro-app

This document outlines the frontend architecture, design principles, styling approach, component structure, state management, routing, performance strategies, and testing plan for the Preá Safety Training micro-application. It is written in everyday language to ensure clarity across technical and non-technical audiences.

## 1. Frontend Architecture

### Frameworks and Libraries

- **Next.js (App Router)**: Our core framework, enabling file-based routing, server-side rendering (SSR), static site generation (SSG), and API routes.
- **TypeScript**: Provides type safety throughout the project, reducing runtime errors and improving maintainability.
- **shadcn/ui**: A modern, accessible component library built on Radix UI, offering pre-styled building blocks (buttons, cards, forms).
- **Tailwind CSS v4 + CSS Variables**: A utility-first styling system for rapid UI development and easy theming with custom properties.
- **Supabase Auth Helpers (`@supabase/auth-helpers-nextjs`)**: Handles user authentication via Magic Link, replacing the previous custom auth solution.
- **Supabase Client (`supabase-js`)**: Communicates with the Supabase database for storing quizzes, attempts, and checklist sessions.
- **n8n & GPT API**: Integrated through secure Next.js API routes or Server Actions to automate workflows (importing questions, sending notifications) and generate AI-powered feedback.
- **Docker & Docker Compose**: Ensures a consistent development environment that mirrors production, making it easy to onboard new contributors.

### Support for Scalability, Maintainability, and Performance

- **Component-based design** keeps UI logic modular and reusable, simplifying new feature development.
- **Server Components vs. Client Components**: Critical data-fetching components run on the server for speed, while interactive elements (`"use client"`) run in the browser.
- **SSR/SSG**: Balances fast initial loads (SSG) and dynamic data needs (SSR) to optimize page performance.
- **Environment-based configuration**: Uses `.env.local` for secrets (Supabase keys, n8n webhook URLs, GPT keys), preventing accidental exposure.
- **Containerization** with Docker reduces "it works on my machine" issues and aligns local and production setups.

## 2. Design Principles

- **Usability**: Clear calls to action, concise quiz questions, and a simple checklist flow ensure users complete tasks in under 30 seconds.
- **Accessibility**: All components follow WCAG guidelines—semantic HTML, proper labels, focus states, and keyboard navigation.
- **Responsiveness**: Mobile-first breakpoints and fluid layouts guarantee a smooth experience on phones and tablets, especially on 4G connections.
- **Feedback & Clarity**: Instant feedback on quiz answers, clear error states for network issues, and progress indicators keep users informed.

## 3. Styling and Theming

### Styling Approach

- **Utility-First with Tailwind CSS**: Rapidly compose styles using predefined classes (e.g., `px-4`, `text-center`, `bg-primary`).
- **CSS Variables** for theming: Define color and spacing tokens in `:root` and override in `dark` mode.
- **No BEM/SMACSS**: The utility-first approach removes the need for custom naming conventions.

### Theming

- **Light & Dark Modes**: Toggled via a React Context or local storage flag. Tailwind’s `dark:` variant applies dark colors automatically.
- **Design Style**: Modern flat design—minimal shadows, crisp edges, and bold typography.

### Color Palette

| Name           | Light Mode   | Dark Mode    | Usage                    |
|----------------|--------------|--------------|--------------------------|
| Primary        | #2563EB      | #1E40AF      | Buttons, links, highlights |
| Secondary      | #F59E0B      | #D97706      | Toggles, accents         |
| Background     | #FFFFFF      | #1F2937      | Page backgrounds         |
| Surface        | #F3F4F6      | #374151      | Cards, panels            |
| Text Primary   | #111827      | #F9FAFB      | Main text                |
| Text Secondary | #4B5563      | #D1D5DB      | Subtitles, placeholders  |
| Success        | #16A34A      | #4ADE80      | Positive feedback        |
| Error          | #DC2626      | #F87171      | Alerts, invalid states   |

### Typography

- **Font Family**: Inter, system-stack fallback (`-apple-system, BlinkMacSystemFont, sans-serif`).
- **Sizes**: Tailwind scale (`text-sm` through `text-2xl`), ensuring clear hierarchy.

## 4. Component Structure

- **Folder Layout**:
  - `/app` for pages and layouts (Next.js App Router).
  - `/components` for shared UI elements.
  - `/components/ui` for raw shadcn/ui overrides.
  - `/components/data-table.tsx`, `/components/chart-area-interactive.tsx` for specialized data displays.
- **Reusability**:
  - Small, focused components (e.g., `<QuizQuestion />`, `<ChecklistItem />`) that accept props and emit simple events.
  - Shared utilities like `cn()` in `/lib/utils.ts` to merge class names.
- **Separation of Concerns**:
  - Server Components handle data fetching and pass props down.
  - Client Components handle user interaction (forms, toggles).

## 5. State Management

- **Supabase as Source of Truth**: All quiz and checklist data live in the Supabase database. React components fetch or mutate via the Supabase client.
- **Local State**:
  - `useState` for form inputs and temporary UI state (e.g., selected quiz answers).
  - `useContext` for global state like authentication status and theme preference.
- **Data Fetching**:
  - Next.js Server Components use the built-in `fetch` or Supabase client directly.
  - Client Components can use `useEffect` + Supabase helpers to refresh data after mutations.

## 6. Routing and Navigation

- **App Router**:
  - File-based routes under `/app` (e.g., `/app/sign-in/page.tsx`, `/app/dashboard/page.tsx`, `/app/dashboard/history/page.tsx`).
  - `layout.tsx` defines common headers, footers, and theme toggle.
- **Linking**:
  - `next/link` for internal navigation.
  - Conditional redirects in Server Components ensure only authenticated users reach the dashboard.

## 7. Performance Optimization

- **Lazy Loading**:
  - Dynamic imports for non-critical components (charts, history table) using `next/dynamic`.
- **Code Splitting**:
  - Automatic with Next.js—each route only loads its needed JS.
- **Asset Optimization**:
  - `next/image` for responsive, optimized images.
  - Purge unused CSS via Tailwind’s built-in tree-shaking.
- **Minimal Client JS**:
  - Keep most logic in Server Components.
  - Only hydrate interactive parts.

## 8. Testing and Quality Assurance

- **Unit Tests**: Jest and React Testing Library for component logic and snapshot tests.
- **Integration Tests**: Test flows like login, quiz submission, and checklist completion using React Testing Library or MSW (Mock Service Worker) to simulate Supabase responses.
- **End-to-End Tests**: Playwright or Cypress to run real-browser scenarios (mobile viewport) covering critical paths.
- **Linting & Formatting**:
  - ESLint with TypeScript rules and Tailwind plugin.
  - Prettier for consistent code style.
- **Continuous Integration**:
  - GitHub Actions or similar to run tests, lint, and type checks on every pull request.

## 9. Conclusion and Overall Frontend Summary

This frontend setup leverages Next.js, TypeScript, shadcn/ui, and Tailwind CSS to deliver a fast, accessible, and mobile-first safety training micro-app. The component-based architecture and clear separation between server and client logic ensure maintainability as the feature set grows. Supabase handles authentication and data storage, while n8n and the GPT API automate content updates and enrich user feedback. Containerized development ensures consistency across environments. Together, these guidelines align with our goals of speed, usability, and reliability—empowering students and instructors to complete safety training in under 30 seconds, even on a 4G connection at the beach.