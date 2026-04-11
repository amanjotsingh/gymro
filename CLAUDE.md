# CLAUDE.md — GymPro Project Context

This file provides context for Claude (or any AI assistant) working on this codebase. Keep it updated as the project evolves.

---

## Project Overview

**GymPro** is a single-file, client-side React web application for gym trainers. It requires no backend, no build step, and no API keys to run. It is designed to be mobile-friendly and deployable to any static host.

---

## Current Implementation

### Delivery Format
- Single self-contained React artifact (JSX compiled at runtime via CDN)
- All state is in-memory (resets on page reload — no persistence layer yet)
- Styling uses CSS custom properties aligned with the claude.ai design system

### State Shape

```js
clients: [
  {
    id: number,
    name: string,
    phone: string,          // E.164 format e.g. "14165550101" for WhatsApp links
    email: string,
    age: number,
    weight: number,         // kg
    height: number,         // cm
    goal: string,           // one of the GOAL_OPTIONS enum
    notes: string,          // health conditions, trainer notes
    joinDate: string,       // YYYY-MM-DD
    changeReq: string|null  // free-text reschedule request from client
  }
]

appointments: [
  {
    id: number,
    clientId: number,
    date: string,           // YYYY-MM-DD
    time: string,           // HH:MM (24hr)
    type: string,           // one of SESSION_TYPES enum
    status: string,         // 'confirmed' | 'pending' | 'cancelled' | 'change-requested'
    notes: string
  }
]

payments: [
  {
    id: number,
    clientId: number,
    amount: number,         // CAD dollars
    date: string,           // YYYY-MM-DD
    desc: string,           // e.g. "Monthly package — April"
    status: string          // 'paid' | 'pending' | 'overdue' | 'partial'
  }
]

groups: [
  {
    id: number,
    name: string,
    description: string,
    clientIds: number[]
  }
]
```

### Enums / Allowed Values

```js
GOAL_OPTIONS = [
  'Weight Loss', 'Muscle Gain', 'Endurance',
  'Flexibility', 'General Fitness', 'Athletic Performance'
]

SESSION_TYPES = [
  'Strength', 'Cardio', 'Flexibility',
  'HIIT', 'General Fitness', 'Rehabilitation'
]

PAYMENT_STATUSES = ['paid', 'pending', 'overdue', 'partial']

APPOINTMENT_STATUSES = ['confirmed', 'pending', 'cancelled', 'change-requested']
```

---

## Views / Navigation

| View | Route key | Description |
|---|---|---|
| Login | — | Shown before auth. Credentials: trainer / gym123 |
| Dashboard | `dashboard` | Stats, today's schedule, alerts (change reqs, overdue payments) |
| Appointments | `appointments` | Full list with filters, booking, confirm/cancel, WhatsApp reminder, PDF export |
| Clients | `clients` | Grid of client cards; drill into profile with metrics, history, payments |
| Groups | `groups` | Create groups, compose and send WhatsApp messages per member |
| Payments | `payments` | Invoice tracking, revenue summary, mark paid, PDF export |

---

## Messaging Architecture

### Current (Free)
WhatsApp deep links using `wa.me/{phone}?text={encodedMessage}`. Opens WhatsApp Web or the mobile app with the message pre-filled. The trainer manually taps send. No API key or account required.

```js
window.open(`https://wa.me/${client.phone}?text=${encodeURIComponent(message)}`, '_blank');
```

### Upgrade Path (Paid / Automated)
Replace `window.open` calls with a POST to a backend endpoint that calls the Twilio SMS API.

```
POST /api/send-sms
{ to: "+14165550101", body: "Your session is tomorrow at 9am!" }
```

Twilio pricing: ~$0.0079 USD per SMS. Requires a Twilio account and phone number.

---

## PDF Export

Powered by **jsPDF 2.5.1** loaded from `cdnjs.cloudflare.com`.

Three export functions exist:
- `exportApptPDF(appts, clients)` — full appointment schedule grouped by date
- `exportClientPDF(client, appts, payments)` — single client profile with history
- `exportPaymentsPDF(payments, clients)` — payment report with revenue summary

All are triggered client-side; no server is involved.

---

## Auth

Currently hardcoded for demo purposes:
- Username: `trainer`
- Password: `gym123`

To productionise: replace the `isLoggedIn` boolean with a JWT or session token validated against a backend auth endpoint (e.g. Supabase Auth, Firebase Auth, or a custom Express route).

---

## BMI Calculation

```js
bmi = weight_kg / (height_m ** 2)
// displayed to 1 decimal place
```

Calculated inline in the Clients view; not stored in state (derived value).

---

## Known Limitations / Tech Debt

- **No persistence** — all data resets on page reload. Next step: integrate localStorage, IndexedDB, or a backend (Supabase recommended for simplicity).
- **No real auth** — hardcoded credentials. Safe for demo only.
- **WhatsApp group broadcast** — no true broadcast; the trainer must click send per member. True bulk messaging requires WhatsApp Business API (Meta approval needed).
- **Single trainer** — no multi-user or role support yet.
- **No recurring appointments** — each session must be booked individually.
- **Currency hardcoded** — displayed as `$` (CAD assumed). Internationalisation not implemented.

---

## Planned Features (Backlog)

- [ ] Persistent storage via localStorage or Supabase
- [ ] Real authentication (Supabase Auth or JWT)
- [ ] Recurring appointments (weekly / bi-weekly patterns)
- [ ] Session notes history per appointment
- [ ] Progress charts — weight, BMI, and goal metrics over time
- [ ] Automated SMS reminders via Twilio (scheduled, not manual)
- [ ] Multi-trainer support with role-based access
- [ ] Google Calendar / iCal sync
- [ ] Client-facing self-service booking portal
- [ ] Invoice PDF emailed directly to client

---

## Dev Notes for Claude

- The entire app is one React component tree rendered via `ReactDOM.createRoot`. Do not split into separate files unless the user explicitly requests a build-tool setup.
- All modals use a fixed-position overlay pattern. Keep modals as separate named components (e.g. `BookModal`, `AddPaymentModal`) for readability.
- Avatar colours are assigned by `index % 5` using the `AV_COLORS` array — do not make them random or they will flicker on re-render.
- When adding new views, also add them to both `Sidebar` (desktop) and `MobileNav` (mobile) components.
- When adding new state shapes, update the **State Shape** section of this file.
- PDF exports should be added as standalone functions (not components) named `export{ViewName}PDF`.
- WhatsApp phone numbers must be stored without `+` or spaces (E.164 digits only) for `wa.me` links to work correctly.

---

## Project File Structure

```
gymro/
├── index.html      ← entire app (HTML + CSS + JS, self-contained)
├── README.md       ← user-facing documentation
├── CLAUDE.md       ← this file — AI/dev context
└── .gitignore      ← standard ignores, Vercel-ready
```

No build step, no package.json, no node_modules. All dependencies load from CDN at runtime:
- `unpkg.com` → React 18
- `cdnjs.cloudflare.com` → jsPDF 2.5.1

---

## Hosting

### Current: GitHub Pages
- Repo must be **public**
- Enable under Settings → Pages → Deploy from branch → main → / (root)
- URL format: `https://{username}.github.io/{repo-name}`

### Future: Vercel
- Import the same GitHub repo at vercel.com
- Zero config needed for static sites
- Upgrade path: when adding Supabase backend, create an `/api` folder with serverless functions — Vercel handles routing automatically
- Recommended project structure for Vercel + backend:
  ```
  gymro/
  ├── index.html        ← or migrate to /public/index.html
  ├── api/
  │   ├── send-sms.js   ← Twilio SMS endpoint
  │   └── auth.js       ← JWT auth endpoint
  ├── package.json
  └── vercel.json
  ```

---

## Last Updated

April 2026 — v2 includes Login, Payment Tracking, and PDF Export features.
