# GymPro — Gym Trainer Management App

A mobile-friendly web application for gym trainers to manage appointments, clients, groups, payments, and send WhatsApp reminders — all from one dashboard.

---

## Features

### Dashboard
Today's schedule at a glance, upcoming appointments, live stats, and a highlighted panel for any pending change requests from clients.

### Appointments
Book sessions, filter by status (upcoming / today / pending / change requests), confirm or cancel with one click, and send WhatsApp reminders instantly.

### Clients
Full profile cards with age, weight, height, BMI (auto-calculated), goal, notes, and appointment history. Add new clients or edit existing ones, including their pre-joining goal in their own words.

### Groups
Create client groups (e.g. "Morning Crew"), compose a message, and send it to each member individually via WhatsApp with one click per person.

### WhatsApp Reminders (Free!)
Every reminder and group message uses `wa.me` links — completely free, no API key needed. It opens WhatsApp with the message pre-filled. The trainer just hits send. When you're ready to upgrade to automated bulk SMS, Twilio is the natural next step (~$0.0079/message).

### Client Change Requests
Clients can flag a reschedule request (stored on their profile), which surfaces as a warning banner on the dashboard and in the appointments tab with a one-click reschedule flow.

### Login Screen
Secure trainer login portal. Demo credentials: username `trainer` / password `gym123`.

### Payment Tracking
Track fees and invoices per client. View total earned, pending, and overdue amounts. Add payments, mark as paid, and export a full payment report to PDF.

### Export to PDF
Export the full appointment schedule, individual client profiles (with metrics and history), or the complete payment report — all powered by jsPDF, no server required.

### Mobile Friendly
At smaller screen widths, the sidebar collapses and a bottom navigation bar appears, making the app fully usable on phones and tablets.

---

## Tech Stack

| Layer | Technology |
|---|---|
| UI Framework | React 18 (via CDN, no build step) |
| Styling | CSS custom properties + responsive grid |
| PDF Export | jsPDF 2.5.1 |
| Messaging | WhatsApp `wa.me` deep links (free) |
| Hosting | Any static host (no backend required) |

---

## Messaging: Free vs Paid

| Option | Cost | Setup | Best for |
|---|---|---|---|
| WhatsApp `wa.me` links | Free | None | Manual per-message sending |
| Twilio SMS | ~$0.0079/msg | API key + account | Automated bulk reminders |
| WhatsApp Business API | Varies | Meta approval required | High-volume broadcasts |

The app currently uses `wa.me` links. To upgrade to automated SMS, integrate [Twilio](https://www.twilio.com/) by replacing the `window.open` calls with a backend API endpoint.

---

## Hosting

### Option 1 — GitHub Pages (current)

1. Create a free account at [github.com](https://github.com)
2. Click **New repository** → name it `gymro` → set to **Public**
3. Upload these files:
   ```
   index.html
   README.md
   CLAUDE.md
   .gitignore
   ```
4. Go to **Settings → Pages → Source → Deploy from branch → main → / (root)**
5. Your app is live at `https://yourusername.github.io/gymro`

> **Custom domain**: Buy a domain (e.g. Namecheap ~$10/yr), add it under Settings → Pages → Custom domain. GitHub handles SSL automatically.

---

### Option 2 — Vercel (recommended for future upgrade)

1. Push the repo to GitHub (same steps above)
2. Go to [vercel.com](https://vercel.com) → **Import Project** → connect your GitHub repo
3. No config needed — Vercel detects a static site automatically
4. Live at `gymro.vercel.app` in ~30 seconds

When you add a backend (Supabase, API routes, etc.), Vercel handles it natively with zero extra config.

---

## Getting Started

1. Open the app in any modern browser.
2. Sign in with your trainer credentials.
3. Add your clients under the **Clients** tab.
4. Book appointments from the **Appointments** tab or via the **+ Book** button on the dashboard.
5. Create groups under **Groups** to send broadcast messages.
6. Track payments under **Payments** and export reports as needed.

---

## Roadmap / Planned Features

- [ ] Recurring appointments (weekly/bi-weekly)
- [ ] Session notes history per client
- [ ] Progress charts (weight, BMI over time)
- [ ] Automated SMS reminders via Twilio
- [ ] Multi-trainer support
- [ ] Google Calendar sync
- [ ] Client-facing booking portal

---

## License

MIT — free to use and modify.
