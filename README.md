# StudioOS

A DAW-inspired personal dashboard for tracking music production, app development, fitness, and reading — built as a single-page web app with Google Calendar sync.

**Live:** `https://studioos-frontend-ten.vercel.app`  
**Backend:** `https://studioos-backend-production.up.railway.app`

---

## What it does

StudioOS is a monthly creative planner. At the start of each month you get a fresh workspace. At the end of the month you archive completed projects to the Hall of Fame and choose what to do with unfinished ones — carry them over, archive as-is, or drop them.

---

## Modules

### Music
11-step mastering workflow per track: `Pre-Prod → Tracking GT → Tracking Bass → Drum Edit → Guitar Edit → Vocal Track → Comping → Mixing → Master Prep → Mastering → QC/Delivery`

- Click any segment to advance progress
- Drag tracks to reorder
- Session notes per track with timestamps
- Drag Google Calendar events onto tracks to set deadlines
- Pre-Flight checklist (13 items) before archiving a release
- Deadline badges: blue (on time) → orange (≤2 days) → red (overdue)

### App Dev
8-step sprint board per project: `Backlog → Planning → Design → Frontend → Backend → Integration → Testing → Deploy`

- Same drag-to-deadline and notes features as Music
- Pre-Flight checklist (6 items) before archiving a release

### Fitness
- 7-day weekly grid (Mon–Sun)
- Adjustable weekly target (1–7 days)
- Auto-resets every Monday
- Workout notes log

### Reading
- Page progress slider per book
- Completion detection
- Carries forward to next month with page position intact

### Hall of Fame
- Holographic gold cards for completed projects
- Liner notes: LUFS/sample rate for music, version/commits/deploy URL for apps
- Re-open any archived project back to the active workspace

---

## Google Calendar

One-way sync from Google Calendar to StudioOS. Fetches all calendars under your Google account for the next 7 days on every app open.

- Bottom bar shows 7 day columns (Today through +6 days)
- Color-coded per calendar
- Drag any event onto a track/project to set it as a deadline
- Click a deadline badge to remove it

---

## Monthly Workflow

1. Work through the month — add tracks, apps, books, log workouts
2. Click **↺ New Month** when you're ready to roll over
3. Completed items (100%) go to Hall of Fame automatically
4. For each unfinished item choose: **Carry over** (keeps current progress) / **Archive as-is** / **Drop**
5. New month starts clean with carried items pre-loaded

---

## Tech Stack

| Layer | Service | Notes |
|-------|---------|-------|
| Frontend | Vercel (free) | Static HTML/CSS/JS, no framework |
| Backend | Railway (~$5/mo) | Node.js + Express |
| Database | PostgreSQL (Railway) | Users, state, snapshots, calendar tokens |
| Calendar | Google Calendar API | OAuth 2.0, read-only scope |

---

## Repo Structure

```
studioos-frontend/
├── index.html          ← Landing page
├── app/
│   └── index.html      ← Dashboard app
├── vercel.json         ← Static site config
└── README.md
```

---

## Backend Repo

The backend lives in a separate repo (`studioos-backend`) deployed on Railway. It handles:

- `POST /auth/login` — password login, returns JWT
- `GET /state` / `POST /state` — load/save full app state
- `GET /snapshots` / `POST /snapshots` — monthly archives
- `GET /calendar/auth-url` — Google OAuth initiation
- `GET /auth/google/callback` — OAuth callback
- `GET /calendar/events` — fetch + cache 7-day events
- `GET /calendar/status` — check if connected
- `DELETE /calendar/disconnect` — remove Google tokens

---

## Environment Variables (Railway backend)

```
DATABASE_URL          → auto-set by Railway PostgreSQL
JWT_SECRET            → long random string
NODE_ENV              → production
GOOGLE_CLIENT_ID      → from Google Cloud Console
GOOGLE_CLIENT_SECRET  → from Google Cloud Console
GOOGLE_REDIRECT_URI   → https://studioos-backend-production.up.railway.app/auth/google/callback
FRONTEND_URL          → https://studioos-frontend-ten.vercel.app
```

---

## Local Development

```bash
# Backend
cd studioos-backend
cp .env.example .env   # fill in your values
npm install
node server.js         # runs on http://localhost:3001

# Frontend — just open app/index.html in a browser
# API_URL auto-detects localhost when running locally
```

---

## Adding a User

User registration is disabled in the UI. Add yourself directly via the Railway database console:

```sql
-- First generate a bcrypt hash at bcrypt-generator.com (rounds: 12)
INSERT INTO users (email, password_hash)
VALUES ('you@example.com', '$2a$12$your-hash-here');
```

---

## Android / Mobile

Open the app URL in Chrome on Android → three-dot menu → **Add to Home screen**. Runs as a PWA-like experience from your home screen.
