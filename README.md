# Brightloop Billing

A subscription management demo: plans, subscriptions, automatic invoicing, discounts, payments (with a simulated "Resso Pay" checkout) and a finance overview.

## Run it
Open `index.html` in a browser, or host it free with GitHub Pages (Settings > Pages > deploy from `main`).

## Demo logins
| Role | Email | Password |
|---|---|---|
| Admin | admin@brightloop.com | Admin@1234! |
| Staff | staff@brightloop.com | Staff@1234! |
| Customer | customer@demo.com | Customer@123! |

## Try this flow
1. Admin: Subscriptions > New subscription > pick "Business starter pack" > Send as quotation > Confirm > Activate.
2. Run billing: draft invoices appear once (running again creates no duplicates). Confirm one.
3. Customer: sign in, open My invoices, pay with Resso Pay (card 4242 4242 4242 4242, OTP 123456).
4. Overview shows monthly recurring revenue, collected, owed and overdue.

## Notes
- Data is stored in the browser (localStorage). It is a front-end demo; a production version needs a server and database (see the Django roadmap).
- Passwords are hashed (SHA-256), inputs are escaped, and every admin action re-checks the user's role.
- Resso Pay is simulated. No real payments are taken.

## Files
- `index.html` is the public website (plans, pricing calculator, how a billing month works).
- `app.html` is the working billing app (sign in, subscriptions, invoices, payments, overview).
- `SECURITY.md` lists the security controls, known limits and the production checklist.
