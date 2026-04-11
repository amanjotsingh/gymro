# CLAUDE.md — GymPro Technical Context

This file is for AI assistants and developers working on this codebase. Update it when adding new features.

---

## Project Overview

GymPro is a single-file React web app for a bootcamp gym trainer. No backend, no build step, no package.json. Everything ships in one `index.html`. Data persists via browser localStorage.

---

## File Structure

```
gymro/
├── index.html    ← entire app
├── README.md     ← user-facing docs
├── CLAUDE.md     ← this file
└── .gitignore
```

---

## Key Constants (top of <script>)

```js
const WA_ENABLED = false;     // flip to true to enable WhatsApp buttons
const SLOT_TIMES = ['06:00','07:00','08:00','09:00','16:00','17:00','18:00','19:00'];
const DEF_NAMES  = ['Early Birds','Rise & Grind','Morning Power','Peak Performance',
                    'Afternoon Burn','Rush Hour Fit','Sunset Sweat','Night Owls'];
```

---

## State Shape

```js
clients: [{
  id, name, phone,        // phone: E.164 digits only e.g. "14165550101"
  email, age, weight,     // weight: kg
  height,                 // height: cm
  goal, notes, joinDate,  // joinDate: YYYY-MM-DD
  changeReq               // string | null — reschedule request text
}]

sessions: [{
  id, name,               // default name from DEF_NAMES, overrideable via snames
  clientIds: number[],    // array of client IDs enrolled in this class
  date,                   // YYYY-MM-DD
  time,                   // HH:MM (24hr)
  type: 'Bootcamp',
  status: 'confirmed',
  isGroup: true
}]

payments: [{
  id, clientId, amount,   // amount: dollars (number)
  date, desc, status      // status: 'paid' | 'pending' | 'overdue'
}]

photos: {
  [clientId]: {
    before: string|null,  // base64 data URL
    after:  string|null,
    beforeDate: string,   // YYYY-MM-DD
    afterDate:  string
  }
}

snames: {
  [sessionId]: string     // custom name overriding session.name
}
```

---

## localStorage Keys

```js
gymro_clients   → clients array
gymro_sessions  → sessions array
gymro_payments  → payments array
gymro_photos    → photos object (can be large if many base64 images)
gymro_snames    → custom session name overrides
```

---

## Data Generation

Clients and sessions are generated **deterministically** on first load using a seeded LCG random function `sr(seed)`. This ensures the same 90 clients and schedule appear every time on a fresh browser. Once persisted to localStorage, the generated data is never regenerated.

```js
genClients()   → 90 clients from FN[] × LN[] name arrays
genSessions()  → Mon–Fri sessions for -21 to +35 days from today
                 8 slots/day × ~7 weeks = ~280 sessions
```

---

## Session Block & Tooltip

`SessionBlock` component renders inline in the calendar. It has:
- An editable name (click ✎ → inline input → Enter to save via `renameSession(id, name)`)
- A CSS hover tooltip showing all enrolled clients in a 2-column grid
- Tooltip flips left on rightmost columns to avoid overflow

Parent calendar containers use `overflow: visible` so the tooltip is never clipped.

---

## Progress Card (Canvas)

`genProgressCard(client, beforeDataUrl, afterDataUrl)` returns a Promise<string> (base64 JPEG). It draws a 1080×1080 canvas with:
- Dark background (#111)
- "TRANSFORMATION" heading
- Client name + goal
- Two rounded image regions (before | after) with overlay labels
- GymPro branding footer

`shareCard(dataUrl, name)` tries `navigator.share({files: [...]})` first (mobile), falls back to `<a download>` click (desktop).

---

## WhatsApp

All WhatsApp buttons use the `WaBtn` component. When `WA_ENABLED = false`, they render as greyed-out disabled buttons with "coming soon" tooltip. When `true`, they open `https://wa.me/{phone}?text={msg}`.

Phone numbers must be stored without `+` or spaces (E.164 digits only).

**Upgrade path to automated SMS:**
Replace `window.open(wa.me...)` calls with a POST to a backend endpoint:
```js
POST /api/send-sms
{ to: "+14165550101", body: "Reminder: class at 6am tomorrow!" }
```
Use Twilio Node.js SDK on the backend. ~$0.0079 USD/SMS.

---

## Auth

Hardcoded: `trainer` / `gym123`. Safe for demo/solo use only.

To productionise: replace the `loggedIn` boolean with a JWT validated against Supabase Auth or a custom Express endpoint.

---

## Hosting

| Platform | Status | Notes |
|---|---|---|
| GitHub Pages | Current | Static only, free |
| Vercel | Planned | Add `/api` folder for serverless functions when backend needed |
| Supabase | Future DB | Replace usePS hooks with Supabase client calls |

---

## Known Limitations

- **No persistence across devices** — localStorage is browser/device-specific
- **No real auth** — hardcoded credentials
- **Photos hit localStorage limit** — base64 images are large; ~5MB browser limit. If storing many photos, migrate photos to Supabase Storage
- **WhatsApp group broadcast** — no true broadcast; trainer clicks send per member. True bulk needs WhatsApp Business API (Meta approval required)
- **Session generation range** — sessions generated for -21 to +35 days. Navigating far outside this range shows empty calendar

---

## Dev Conventions

- All components use `React.createElement` (no JSX/Babel needed)
- `usePS(key, init)` = persisted state hook (useState + localStorage sync)
- `ws(setter, label)` = wraps a setter to show a toast on every change
- Add new nav items to both `Sidebar` (desktop) and `MobileNav` (mobile)
- New persisted state needs a key in the `LS` constant object
- PDF export functions are named `export{ViewName}PDF` and live outside components
- `WA_ENABLED` is a single constant — never inline-check for WhatsApp availability

---

## Last Updated

April 2026 — v4: bootcamp sessions, 90 clients, calendar views with hover tooltips, before/after photos, Canvas progress card, Instagram/social sharing, WhatsApp disabled flag.
