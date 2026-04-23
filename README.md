# 🚀 Clarity CRM — Full Setup & Deployment Guide

A modern, production-ready CRM for managing contacts and your sales pipeline.

**Tech Stack:** React + Tailwind CSS · Node.js + Express · Supabase (PostgreSQL + Auth)

---

## 📁 Project Structure

```
crm/
├── frontend/          # React app (deploy to Vercel)
│   ├── public/
│   └── src/
│       ├── components/   # Layout, ContactModal
│       ├── context/      # AuthContext
│       ├── hooks/        # useContacts
│       ├── lib/          # supabase.js, api.js
│       └── pages/        # Dashboard, Contacts, Pipeline, Login, Register
├── backend/           # Express API (deploy to Railway or Render)
│   └── src/
│       ├── config/       # supabase admin client
│       ├── middleware/   # auth.js (JWT verification)
│       └── routes/       # contacts.js, stats.js
└── database/
    └── schema.sql     # Run this in Supabase SQL Editor
```

---

## ✅ STEP 1 — Create Your Supabase Project

1. Go to **https://supabase.com** and sign up (free)
2. Click **"New project"** → give it a name (e.g. `clarity-crm`)
3. Choose a region close to you → click **"Create new project"**
4. Wait ~2 minutes for it to provision

### Run the Database Schema

5. In your Supabase dashboard, click **"SQL Editor"** in the left sidebar
6. Click **"New query"**
7. Copy the entire contents of `database/schema.sql` and paste it in
8. Click **"Run"** — you should see "Success. No rows returned"

### Collect Your Supabase Keys

Go to **Settings → API** in your Supabase dashboard and copy:

| Key | Where to find it |
|-----|-----------------|
| `SUPABASE_URL` | "Project URL" |
| `SUPABASE_ANON_KEY` | "anon / public" key |
| `SUPABASE_SERVICE_KEY` | "service_role" key (keep secret!) |
| `SUPABASE_JWT_SECRET` | Settings → API → JWT Settings → "JWT Secret" |

---

## ✅ STEP 2 — Run Locally (Development)

### Backend Setup

```bash
cd crm/backend
npm install

# Create your env file
cp .env.example .env
```

Edit `backend/.env`:
```
PORT=4000
SUPABASE_URL=https://xxxxxxxxxxxx.supabase.co
SUPABASE_SERVICE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
SUPABASE_JWT_SECRET=your-jwt-secret-from-supabase-settings
FRONTEND_URL=http://localhost:3000
```

Start the backend:
```bash
npm run dev
# ✅ CRM API running on http://localhost:4000
# Test: http://localhost:4000/health
```

### Frontend Setup

Open a **new terminal**:

```bash
cd crm/frontend
npm install

# Create your env file
cp .env.example .env
```

Edit `frontend/.env`:
```
REACT_APP_SUPABASE_URL=https://xxxxxxxxxxxx.supabase.co
REACT_APP_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
REACT_APP_API_URL=http://localhost:4000/api
```

Start the frontend:
```bash
npm start
# Opens http://localhost:3000
```

### Test It Works

1. Go to `http://localhost:3000/register`
2. Create an account with your email
3. Check your email for the confirmation link and click it
4. Sign in at `http://localhost:3000/login`
5. You should see the dashboard!

---

## ✅ STEP 3 — Deploy the Backend (Railway)

Railway is the easiest free option for Node.js APIs.

1. Go to **https://railway.app** and sign up with GitHub
2. Click **"New Project"** → **"Deploy from GitHub repo"**
3. Select your repo (push your code to GitHub first)
4. Railway auto-detects Node.js

### Set Environment Variables on Railway

In your Railway project → **Variables** tab, add:

```
PORT=4000
SUPABASE_URL=https://xxxxxxxxxxxx.supabase.co
SUPABASE_SERVICE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
SUPABASE_JWT_SECRET=your-jwt-secret
FRONTEND_URL=https://your-app.vercel.app
```

### Set Root Directory

In Railway → Settings → **"Root Directory"** → set to `backend`

5. Railway gives you a URL like `https://crm-backend-production.up.railway.app`
6. Test it: visit `https://your-railway-url/health` — should return `{"status":"ok"}`

---

## ✅ STEP 3 (Alternative) — Deploy Backend to Render

1. Go to **https://render.com** → sign up
2. Click **"New"** → **"Web Service"**
3. Connect your GitHub repo
4. Configure:
   - **Root Directory:** `backend`
   - **Build Command:** `npm install`
   - **Start Command:** `npm start`
   - **Plan:** Free

5. Add the same environment variables as above in **"Environment"** tab
6. Click **"Create Web Service"**

> ⚠️ Note: Render free tier spins down after 15 min of inactivity (first request is slow). Upgrade to paid to avoid this.

---

## ✅ STEP 4 — Deploy the Frontend (Vercel)

1. Go to **https://vercel.com** → sign up with GitHub
2. Click **"Add New Project"**
3. Import your GitHub repo
4. Configure:
   - **Root Directory:** `frontend`
   - **Framework Preset:** Create React App (auto-detected)

### Set Environment Variables on Vercel

In Vercel project settings → **Environment Variables**, add:

```
REACT_APP_SUPABASE_URL=https://xxxxxxxxxxxx.supabase.co
REACT_APP_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
REACT_APP_API_URL=https://your-railway-or-render-url/api
```

5. Click **"Deploy"**
6. Vercel gives you a URL like `https://clarity-crm.vercel.app`

### Update CORS on Backend

Go back to Railway/Render and update `FRONTEND_URL` to your Vercel URL:
```
FRONTEND_URL=https://clarity-crm.vercel.app
```

---

## ✅ STEP 5 — Configure Supabase Auth (Important!)

1. In Supabase → **Authentication → URL Configuration**
2. Set **"Site URL"** to your Vercel URL: `https://clarity-crm.vercel.app`
3. Add to **"Redirect URLs"**: `https://clarity-crm.vercel.app/**`
4. Click **Save**

This ensures email confirmation links redirect to your live app.

---

## 🧩 Features Overview

| Feature | Description |
|---------|-------------|
| 🔐 Auth | Register/Login with email via Supabase Auth |
| 📊 Dashboard | Summary stats (total, leads, proposals, closed) + recent contacts |
| 👥 Contacts | Add, edit, delete contacts with name, email, phone, status, notes |
| 🔍 Search | Real-time search across name, email, phone |
| 🏷️ Status | Lead → Contacted → Proposal → Closed → Inactive |
| 📋 Pipeline | Kanban board with drag-and-drop between stages |
| 📱 Responsive | Mobile-first design, works on all screen sizes |
| 🔒 Secure | JWT auth, RLS (Row Level Security), rate limiting, CORS |

---

## 🔧 Common Issues & Fixes

### "Invalid token" errors
- Make sure `SUPABASE_JWT_SECRET` in your backend matches the one in Supabase Settings → API → JWT Secret exactly

### Email confirmation not redirecting
- Check Supabase → Auth → URL Configuration and add your Vercel URL

### CORS errors in production
- Update `FRONTEND_URL` in your backend env vars to your exact Vercel URL

### Contacts not loading
- Check `REACT_APP_API_URL` in Vercel env vars points to your backend URL with `/api` at the end
- Example: `https://crm-backend.up.railway.app/api`

### Build fails on Vercel
- Make sure Root Directory is set to `frontend`
- All `REACT_APP_*` env vars must be set before deploying

---

## 🛠️ Local Development Tips

```bash
# Backend with auto-restart
cd backend && npm run dev

# Frontend with hot reload
cd frontend && npm start

# Check backend is running
curl http://localhost:4000/health
```

---

## 📦 Pushing to GitHub (Required for Deployment)

```bash
# From the crm/ root folder
git init
git add .
git commit -m "Initial CRM app"
git branch -M main

# Create a repo on github.com first, then:
git remote add origin https://github.com/yourusername/clarity-crm.git
git push -u origin main
```

---

## 🔮 Next Steps & Scaling Ideas

- **Email notifications** — Use Resend or SendGrid for follow-up reminders
- **Activity log** — Track every status change and note update
- **Tags/Labels** — Group contacts by industry or source
- **CSV import/export** — Bulk add contacts from spreadsheets
- **Team support** — Add role-based access for multiple users
- **Analytics** — Conversion rates, pipeline velocity charts
- **Integrations** — Connect to Gmail, Calendly, or Zapier

---

## 📄 License

MIT — free to use, modify, and deploy for personal or commercial projects.
