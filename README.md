# StudioOS — Vercel Frontend Repo

## Structure
```
/                     ← Landing page (studioos.vercel.app)
  index.html
  vercel.json
  app/
    index.html        ← Dashboard app (studioos.vercel.app/app)
```

## Deploy to Vercel
1. Push this entire folder to a GitHub repo (e.g. `studioos-frontend`)
2. Go to vercel.com → Add New Project → Import that repo
3. **No build settings needed** — Vercel detects `vercel.json` automatically
4. Click Deploy

## After deploying
Open `app/index.html` and replace this line:
```
'https://YOUR-RAILWAY-APP.up.railway.app'
```
With your actual Railway backend URL. Commit → Vercel auto-redeploys.

## URLs you'll get
- Landing page: `https://studioos-frontend.vercel.app`
- Dashboard app: `https://studioos-frontend.vercel.app/app`
