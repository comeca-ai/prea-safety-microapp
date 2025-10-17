# Security Guidelines for Preá Safety Micro-App

This document provides actionable security controls and best practices tailored to the `prea-safety-microapp`, a Next.js/TypeScript micro-application using Supabase, n8n, GPT integrations, and containerization. Follow these guidelines to embed security by design across the entire development lifecycle.

---

## 1. Authentication & Access Control

• **Supabase Magic Link**
  - Enforce single-use, time-limited links (e.g., expire after 15 minutes).
  - Rate-limit link generation (e.g., max 5 requests per hour per IP/email) to prevent abuse.
  - Validate domain for redirect URIs and disallow untrusted callback URLs.

• **Role-Based Access & Row-Level Security (RLS)**
  - Define minimal roles (e.g., `authenticated_user`, `admin`).
  - Enable RLS on all user data tables (`quiz_attempts`, `checklist_sessions`) so users only read/write their own rows.
  - Use Supabase service role key exclusively in trusted server-side code; never expose it in client bundles.

• **Session Management & Cookies**
  - Configure session cookies as `HttpOnly`, `Secure`, `SameSite=Strict` or `Lax`.
  - Enforce idle timeouts (e.g., 30 minutes) and absolute timeouts (e.g., 24 hours).
  - Provide explicit logout endpoints that revoke session tokens server-side.

• **Multi-Factor Authentication (MFA)**
  - Plan for optional MFA (e.g., TOTP or SMS) for instructors or admin portals handling sensitive workflows.

## 2. Input Handling & Processing

• **Server-Side Validation**
  - Validate and sanitize all API inputs using a schema-validation library (e.g., Zod).
  - Never trust client-side checks alone; duplicate validation on API routes and Server Actions.

• **Prevent Injection Attacks**
  - Use Supabase client queries (parameterized queries) rather than raw SQL.
  - For n8n and GPT payloads, strictly validate JSON structures; escape or remove dangerous characters.

• **Cross-Site Scripting (XSS)**
  - Default to Next.js automatic escaping. Avoid `dangerouslySetInnerHTML` unless content is sanitized.
  - Implement a strict Content Security Policy (CSP) to restrict script sources.

• **Redirects & Forwards**
  - Maintain an allow-list of valid redirect domains for post-login flows.
  - Reject unrecognized or external URLs.

## 3. Data Protection & Privacy

• **Encryption in Transit & at Rest**
  - Enforce TLS 1.2+ for all web and API communications (Next.js ↔ Supabase, Next.js ↔ n8n/GPT).
  - Confirm that Supabase storage and DB use disk-level encryption.

• **Secrets Management**
  - Store API keys (Supabase anon/service keys, GPT keys, n8n webhooks) in a dedicated vault or environment variables, not in source code.
  - Rotate secrets periodically and upon team member offboarding.

• **PII Minimization & Logging**
  - Log only operational data (status codes, timestamps), never full user emails or responses.
  - Mask or redact identifiers if stored in logs.

## 4. API & Service Security

• **HTTPS Enforcement**
  - Redirect all HTTP traffic to HTTPS in production (e.g., `next.config.js` or reverse proxy).

• **CORS Configuration**
  - Restrict CORS to known origins (e.g., your beach signage domain and authorized admin domains).
  - Disallow wildcard `*` in production.

• **Rate Limiting & Throttling**
  - Implement per-IP or per-user rate limits on sensitive endpoints (quiz submission, magic-link generation) using middleware (e.g., rate-limit package).

• **Least Privilege in Cloud Functions**
  - Limit n8n workflows and webhooks to only the scopes they require (read questions, write quiz_attempts).

## 5. Web Application Security Hygiene

• **CSRF Protection**
  - Use anti-CSRF tokens for state-changing requests that rely on cookies (e.g., checklist submissions).

• **Security Headers**
  - `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`
  - `X-Content-Type-Options: nosniff`
  - `X-Frame-Options: DENY`
  - `Referrer-Policy: no-referrer-when-downgrade`
  - `Content-Security-Policy: frame-ancestors 'none'; default-src 'self' ...`

• **Cookie Flags**
  - Mark tracking or session cookies as `SameSite=Strict` or `Lax`, `Secure`, `HttpOnly`.

• **Subresource Integrity (SRI)**
  - When loading any third-party scripts or styles (CDNs), include SRI hashes.

## 6. Infrastructure & Configuration Management

• **Docker Hardening**
  - Use minimal base images (e.g., `node:alpine`).
  - Run application processes as non-root users inside containers.
  - Scan container images regularly for vulnerabilities.

• **Environment Configuration**
  - Keep `.env.local` out of version control; enforce a sample `.env.example`.
  - Validate presence of required env vars at startup; fail fast if missing.

• **Dependency & OS Patching**
  - Automate patching of underlying OS and package dependencies.
  - Monitor and apply Next.js, Supabase-JS, Tailwind, and `shadcn/ui` updates.

• **Disable Debug in Production**
  - Set `NODE_ENV=production`; disable source maps and verbose error stacks.

## 7. Dependency Management

• **Lockfiles & Auditing**
  - Commit `package-lock.json` or `yarn.lock` and run `npm audit` in CI.
  - Integrate automated SCA tools (Dependabot, Snyk) to catch CVEs.

• **Minimize Third-Party Footprint**
  - Remove deprecated or unused modules (e.g., `drizzle-orm`, `better-auth`).
  - Vet new libraries for active maintenance and security track record.

## 8. CI/CD & Monitoring

• **Secure CI Secrets**
  - Store keys in encrypted CI variables, restrict read/write to maintainers.

• **Automated Testing & Scanning**
  - Include linting, type checks, unit/integration tests, and vulnerability scans as pipeline gates.

• **Runtime Monitoring**
  - Capture errors with a centralized service (e.g., Sentry) and redact sensitive fields.
  - Monitor performance, API error rates, and authentication failures.

---

By integrating these controls into your Next.js development workflow, you ensure a defense-in-depth posture that protects user data, maintains service integrity, and minimizes risk. Regularly revisit and update these guidelines as your application and threat landscape evolve.