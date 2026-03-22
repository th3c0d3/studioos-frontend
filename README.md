# StudioOS

**Song Production Process Management — and everything else that comes with the creative life.**

StudioOS is a DAW-inspired productivity workspace built for musicians, developers, and creators who want to manage their work with the precision of a professional mixing board. Track every production step, every workout, every page, every sprint — all in one focused app.

Live at **[studioos-frontend-ten.vercel.app](https://studioos-frontend-ten.vercel.app)**

---

## What it is

StudioOS started as a music production tracker and grew into a full creative OS. The core idea: your music career doesn't exist in isolation. You're also building apps, staying fit, reading books, planning your days. StudioOS gives all of that a single home with a consistent interface.

---

## Modules

### 🎵 Music — Production Pipeline
The centrepiece. An 11-step mastering workflow that maps your entire production process from first scratch to distributor upload.

- Segmented step bar: Pre-Production → Tracking GT → Tracking Bass → Drum Edit → Guitar Edit → Vocal Track → Comping → Mixing → Master Prep → Mastering → QC/Delivery
- Custom step names per track — rename any step to match your workflow
- BPM, Key, Song Type (With Lyrics / Instrumental / Partial Lyrics / Melody Only)
- Original / Cover badge
- Lyrics editor — popup modal with word and line counter
- Session notes with checkboxes, timestamps, done state
- Drag to reorder tracks
- Inline rename with ✎ pencil
- Deadline badges — drag Google Calendar events onto tracks to set deadlines
- Focus Mode — full-screen single-track view with sidebar navigation
- QC/Delivery checklist — 11-item production checklist (Scratch & Structure → Tracking → Editing → Mix → Mastering → Delivery) that locks the Archive button until every item is checked
- Hall of Fame — completed tracks get a gold vinyl card with liner notes and completion timestamp
- Celebration animation — spinning vinyl + particle burst on 100%, gold version on archive

### ⚡ App Dev — Sprint Board
- 8-step sprint board from Planning to Deployment
- Session notes with checkboxes
- Focus Mode
- Pre-flight QA checklist before shipping

### ◎ Fitness — Weekly Tracker
- 7-day weekly grid with adjustable target
- Workout logging with calorie tracking (kcal per session)
- Inline rename for workout names
- Total calorie summary
- Auto-reset every Monday

### ◻ Reading — Page Tracker
- Page-by-page progress slider per book
- Completion detection
- Monthly snapshot

### ◈ Planner — Daily Productivity Hub
- **Day / Week toggle** — compact 7-day overview (click any day to expand inline detail) or full day view
- Primary Goals — unlimited, persist indefinitely
- Today's Agenda — time-blocked slots sorted by time
- Tasks Today — checkboxes, auto-promotes from Tomorrow at midnight
- Tasks Tomorrow — auto-moves to Today at midnight
- Priority Matrix — 4-quadrant Eisenhower matrix with draggable cards, inline text input per quadrant, ✓ to archive
- Notes & Ideas — freeform textarea
- Day navigation — Yesterday (read-only), Today, Tomorrow
- Section filters — toggle any section visible/hidden by colour
- Yesterday is fully read-only

### ◈ Stats — Data & Insights
- **Weekly view**: active track progress bars, fitness week grid with kcal, auto-generated insights
- **Monthly view**: all tracks with progress, fitness summary, reading progress, monthly insights
- Pulls live from current state — no extra storage needed

---

## Key Features

### Global Search
Press `/` anywhere or click ⌕ to search across all tracks, projects, notes, books, and archived months. Filter by category. Jumps directly to the right tab.

### Google Calendar Integration
Connect your Google Calendar via OAuth. A 7-day event bar appears at the bottom. Drag any event onto a music track to set it as a deadline — the track shows a colour-coded badge that turns orange then red as the date approaches.

### Monthly Carry-Over
At the end of each month, a carry-over modal shows every unfinished item. Choose Carry Over, Archive As-Is, or Drop for each one. Monthly snapshots are preserved forever in History.

### Four Themes
Cycle with the ◐ button — saved and synced across devices.
- **Dark** — deep charcoal, green accents (default)
- **Light** — soft grey, dark green accents
- **C64** — deep blue, phosphor green, Share Tech Mono
- **Amiga** — grey workbench, Amiga blue

### Mobile-First on Android
- Swipe left/right between tabs
- Bottom navigation bar (thumb-friendly)
- FAB + action sheet for quick adds
- 44px tap targets on every card action row
- Haptic feedback on step advance and completion

### Hall of Fame
Archived projects get a holographic gold card treatment with liner notes, completion date, days-to-master, and a re-open option. Stats panel shows total tracks released, average completion time, and most recent release.

### Celebration Animations
- **Small (green)** — fires when any track hits 100%. Vinyl spins in, green particles burst from centre, `100% COMPLETE ✓` message appears for 3 seconds.
- **Big (gold)** — fires when a track is archived to the Hall of Fame. Larger gold vinyl, triple particle burst, `ARCHIVED ✦` with the track name.

---

## Architecture

| Layer | Technology |
|---|---|
| Frontend | Static HTML/CSS/JS — deployed on Vercel |
| Backend | Node.js + Express — deployed on Railway |
| Database | PostgreSQL — Railway managed |
| Auth | JWT (30-day expiry) + bcrypt (rounds: 12) |
| Calendar | Google Calendar API — OAuth 2.0, read-only |

### Frontend
Single-file app (`app/index.html`) — no build step, no framework. All state managed in memory and synced to the server on a debounced schedule. Planner data stored in `localStorage` with namespaced keys (`sp_*`).

### Backend API
```
POST /auth/register     — create account (email + password min 6 chars)
POST /auth/login        — returns JWT
GET  /state             — load workspace state
POST /state             — save workspace state
GET  /snapshots         — list monthly snapshots
POST /snapshots         — save monthly snapshot
GET  /calendar/auth-url — begin Google OAuth flow
GET  /auth/google/callback
GET  /calendar/events   — fetch 7-day events
GET  /calendar/status   — connection status
DELETE /calendar/disconnect
```

### State Schema (key fields)
```js
{
  month, nextId, theme,
  songs: [{ id, title, trackType, songType, bpm, key, lyrics,
            steps[], notes[], deadline, deadlineLabel, expanded }],
  apps:  [{ id, title, steps[], notes[], expanded }],
  books: [{ id, title, currentPage, totalPages }],
  fitness: { target, days[], notes[], lastReset },
  archived: [{ ...item, type, archivedAt }],
  checklistState: {}
}
```

---

## Data Persistence

| Data | Storage |
|---|---|
| Workspace state (music, apps, fitness, reading) | PostgreSQL via Railway backend |
| Monthly snapshots | PostgreSQL |
| Planner (goals, notes, matrix) | localStorage `sp_persist_*` |
| Planner tasks & agenda | localStorage `sp_tasks_YYYY-MM-DD` |
| Theme preference | Synced to server state |

---

## Running Locally

### Backend
```bash
cd backend
npm install
# set env vars (see below)
node server.js
```

**Environment variables:**
```
DATABASE_URL=postgresql://...
JWT_SECRET=your-secret
NODE_ENV=development
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...
GOOGLE_REDIRECT_URI=http://localhost:3000/auth/google/callback
FRONTEND_URL=http://localhost:8080
```

### Frontend
No build step needed — just serve `index.html` with any static server:
```bash
npx serve .
# or open app/index.html directly in a browser
```

---

## Backlog

Features discussed and planned but not yet built:

| Feature | Complexity |
|---|---|
| Music track folders / project grouping | Hard |
| Folder rename (✎ pencil) | Easy |
| Step tasks with auto-advance | Hard |
| Pull to refresh sync | Easy |
| Haptic feedback | Easy |
| Collaborator tag per track | Easy |
| Reference track link (YouTube/Spotify) | Easy |
| Spotify link in Hall of Fame | Easy |
| Monthly stats recap on New Month | Easy |
| Days-in-progress counter per track | Easy |
| Daily session timer | Medium |
| Fitness streak counter | Easy |
| Export month as PDF | Hard |
| Auto theme by time of day | Easy |

---

## Stack versions

- Node.js 20
- Express 4
- bcryptjs 2.4
- jsonwebtoken 9
- pg (node-postgres) 8
- Deployed: Railway (backend) + Vercel (frontend)

---

*Built for creators who take their craft seriously.*
