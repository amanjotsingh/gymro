# GymPro — Bootcamp Trainer Management App

A mobile-friendly web application for bootcamp gym trainers to manage group sessions, 90+ clients, payments, progress photos, and social sharing — all from one dashboard. No backend, no build step, runs from a single `index.html`.

---

## Features

### Dashboard
Live stats (today's classes, clients today, revenue, overdue payments) plus a full interactive calendar with three views.

### Calendar — Daily / Weekly / Monthly
- **Daily view** — scrollable hour-by-hour timeline, sessions appear as colour-coded blocks in their time slots
- **Weekly view** — 7-column grid, today highlighted in blue, all sessions visible at a glance
- **Monthly view** — full month grid with session pills per day
- **Hover tooltip** — hovering any session block pops up a card listing every enrolled client by name
- **Rename any class** — click the ✎ pencil icon on any session block and type a new name. Persists in localStorage
- **Navigate** — ‹ › arrows move by day/week/month; "Today" button snaps back to current date

### Bootcamp Sessions (Mon–Fri)
- **Morning block:** 6:00 · 7:00 · 8:00 · 9:00 am
- **Evening block:** 4:00 · 5:00 · 6:00 · 7:00 pm
- Default class names: Early Birds, Rise & Grind, Morning Power, Peak Performance, Afternoon Burn, Rush Hour Fit, Sunset Sweat, Night Owls

### 90 Clients
Generated automatically on first load, evenly distributed across the 8 daily slots (~11 per class). All client data is editable and persisted in localStorage.

### Before & After Photos + Progress Card
- Upload before and after photos on any client profile
- Click "Generate Progress Card" to create a 1080×1080 branded image
- **Mobile**: Web Share API sends the image directly to Instagram or any app
- **Desktop**: Download the card, then open Instagram to post manually

### Payments
Track monthly bootcamp fees. View total, paid, pending, and overdue. Add payments and mark as paid.

### WhatsApp
All buttons are present but **disabled by default**. Set `WA_ENABLED = true` in `index.html` to activate.

### Login
Credentials: `trainer` / `gym123`

### Mobile Friendly
Sidebar collapses on small screens, bottom nav bar appears.

---

## Project Files

```
gymro/
├── index.html    ← entire app (self-contained)
├── README.md     ← this file
├── CLAUDE.md     ← technical context for AI / developers
└── .gitignore
```

---

## Hosting

### GitHub Pages (current)
1. Create a **public** repo on github.com
2. Upload all 4 files
3. Go to **Settings → Pages → Source → Deploy from branch → main → / (root) → Save**
4. Live at `https://yourusername.github.io/gymro`

### Vercel (upgrade path)
Import the same GitHub repo at vercel.com — zero config, deploys in ~30 seconds. Best when adding a backend (Supabase, Twilio API routes, etc.).

---

## Tech Stack

| Layer | Technology |
|---|---|
| UI Framework | React 18 (CDN) |
| Progress Cards | HTML Canvas API |
| PDF Export | jsPDF 2.5.1 |
| Messaging | WhatsApp wa.me links (disabled by default) |
| Persistence | Browser localStorage |
| Hosting | Any static host |

---

## Enabling WhatsApp

In `index.html`, find:
```js
const WA_ENABLED = false; // set true to enable WhatsApp links
```
Change to `true`. All buttons activate immediately.

---

## Roadmap
- [ ] Supabase backend (multi-device sync)
- [ ] Automated SMS reminders via Twilio
- [ ] Client progress charts (weight/BMI over time)
- [ ] Multi-trainer support
- [ ] Client self-service booking portal

---

## License
MIT
