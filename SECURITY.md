# Security

Brightloop Billing handles accounts, invoices and payment records, so security is part of the design. This file explains what is protected, how, and what to change before real customers use it.

## Reporting a problem
Open a private security advisory on this repository (Security tab > Report a vulnerability), or email the maintainers listed in the README. Please don’t post vulnerabilities in public issues. We aim to reply within 3 working days.

## What the current build does
| Area | Control |
|---|---|
| Accounts | One account per email. Passwords need more than 8 characters with an uppercase letter, a lowercase letter and a special character. |
| Password storage | Passwords are hashed (SHA-256 in this demo), never stored as typed. |
| Password reset | Single-use random token (96 bits), expires after 15 minutes, sent to the registered email only. |
| Brute force | Login locks for 30 seconds after 5 failed attempts. |
| Roles | Admin, staff and customer. Every action re-checks the signed-in user’s role. Only the admin can add staff, products, plans, taxes, discounts and templates. |
| Data isolation | Customers can only list, change or pay their own subscriptions and invoices. |
| Injection | All user-entered text is HTML-escaped before it is shown (stops stored XSS). |
| Billing integrity | Each invoice has a unique key (subscription + billing period), so a run can never bill the same period twice or skip one. |
| Payments | Card numbers are validated in the browser and never stored. Only the method, amount, date, note and reference are kept. |
| Sessions | The signed-in user is held in sessionStorage and cleared on sign out or when the tab closes. |

## Known limits of the demo
- It is a front-end demo: data sits in the browser’s localStorage, so anyone with access to that browser can read or edit it. Role checks in browser code are a convenience, not a real barrier.
- Resso Pay is simulated. No real money moves and no real card data should ever be entered.
- SHA-256 without a per-user salt is not enough for production password storage.

## Before going to production (Django version)
1. Move all logic to the server. Enforce roles in every view with permission classes, not in the UI.
2. Hash passwords with Argon2 or bcrypt (Django: `PASSWORD_HASHERS`), plus `AUTH_PASSWORD_VALIDATORS` for the password rules.
3. Serve over HTTPS only: `SECURE_SSL_REDIRECT`, `SESSION_COOKIE_SECURE`, `CSRF_COOKIE_SECURE`, HSTS.
4. Keep secrets (`SECRET_KEY`, database URL, gateway keys) in environment variables, never in Git. Commit `.env.example` only.
5. Use the ORM or parameterised queries only. No string-built SQL.
6. Rate-limit login, signup and reset endpoints (django-axes or django-ratelimit).
7. Use a real payment gateway’s hosted fields so card data never touches your server. Verify payment webhooks by signature, and make them idempotent.
8. Add a Content-Security-Policy header, `X-Content-Type-Options: nosniff` and `X-Frame-Options: DENY`.
9. Write an audit log for invoice, payment and role changes.
10. Add database indexes (status, dates, customer) and pagination so pages stay under two seconds with thousands of records.
11. Back up the database daily and test a restore.
12. Run `pip-audit` or Dependabot, and keep dependencies updated.

## Never commit
`.env`, database files, API keys, gateway secrets, real customer data.
