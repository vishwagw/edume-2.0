# Edume Learning — Deployment Guide
## Vercel + Supabase Setup

---

## Step 1 — Set Up Supabase

### 1.1 Create Your Project
1. Go to https://supabase.com and sign in (or create a free account)
2. Click **"New Project"**
3. Fill in:
   - **Name:** `edume-learning`
   - **Database Password:** choose a strong password (save it!)
   - **Region:** choose `Southeast Asia (Singapore)` — closest to Sri Lanka
4. Click **"Create new project"** — wait ~2 minutes for it to spin up

### 1.2 Run the Database Schema
1. In your Supabase dashboard, go to **SQL Editor** (left sidebar)
2. Click **"New Query"**
3. Open the file `supabase-schema.sql` from this project
4. Paste the entire contents into the SQL editor
5. Click **"Run"** (or press Ctrl+Enter)
6. You should see: "Success. No rows returned"

### 1.3 Get Your API Keys
1. Go to **Project Settings** (gear icon, left sidebar) → **API**
2. Copy these two values — you'll need them in Step 3:
   - **Project URL** (looks like: `https://abcdefghij.supabase.co`)
   - **anon / public key** (long JWT string)

### 1.4 Enable Google Auth (optional but recommended)
1. In Supabase → **Authentication** → **Providers** → **Google**
2. Enable it and follow the Google Cloud Console setup
3. Add your Vercel domain to the redirect URLs once deployed (Step 4.3)

### 1.5 Set Up Storage Buckets
The SQL schema creates storage buckets automatically.
If they don't appear, go to **Storage** → **New Bucket** and create:
- `course-thumbnails` (public ✅)
- `course-videos` (public ❌ — private)
- `course-resources` (public ❌ — private)
- `avatars` (public ✅)

---

## Step 2 — Add Your Supabase Keys to the Project

Open `js/supabase-client.js` and replace these two lines:

```javascript
const SUPABASE_URL = window.__ENV__?.SUPABASE_URL || 'https://YOUR_PROJECT_ID.supabase.co';
const SUPABASE_ANON_KEY = window.__ENV__?.SUPABASE_ANON_KEY || 'YOUR_ANON_KEY';
```

Replace with your actual values:
```javascript
const SUPABASE_URL = 'https://abcdefghij.supabase.co';   // your URL
const SUPABASE_ANON_KEY = 'eyJhbGciOiJ...';              // your anon key
```

> ⚠️ The anon key is safe to expose in frontend code — Supabase is secured by
> Row Level Security (RLS) policies, which are already set up in the schema.

---

## Step 3 — Push to GitHub

```bash
# In your terminal, navigate to the edume-learning folder
cd edume-learning

# Initialize a git repository
git init
git add .
git commit -m "Initial commit — Edume Learning platform"

# Create a new repo on GitHub (github.com → New repository)
# Then push:
git remote add origin https://github.com/YOUR_USERNAME/edume-learning.git
git branch -M main
git push -u origin main
```

---

## Step 4 — Deploy to Vercel

### 4.1 Connect to Vercel
1. Go to https://vercel.com and sign in with GitHub
2. Click **"Add New Project"**
3. Find and select your `edume-learning` repository
4. Click **"Import"**

### 4.2 Configure the Project
On the configuration screen:
- **Framework Preset:** select **"Other"** (since it's plain HTML)
- **Root Directory:** leave as `./`
- **Build Command:** leave empty (no build step needed)
- **Output Directory:** leave empty

Click **"Deploy"** — your site will be live in ~30 seconds!

### 4.3 Update Supabase Redirect URLs
Once deployed, Vercel gives you a URL like `https://edume-learning.vercel.app`

Go back to Supabase → **Authentication** → **URL Configuration**:
- **Site URL:** `https://edume-learning.vercel.app`
- **Redirect URLs:** add `https://edume-learning.vercel.app/**`

### 4.4 Add a Custom Domain (optional)
1. In Vercel → your project → **Settings** → **Domains**
2. Add your domain, e.g. `edumelearning.lk`
3. Follow Vercel's DNS setup instructions
4. Update the Supabase redirect URLs to include your custom domain

---

## Step 5 — Create Your First Admin/Instructor Account

1. Visit your deployed site → click **"Get Started"**
2. Sign up as an **Instructor**
3. Go to Supabase → **Table Editor** → `profiles`
4. Find your row and set `role` to `instructor` if needed
5. Log in at `/login.html` → you'll be redirected to the instructor dashboard

---

## Project File Structure

```
edume-learning/
├── index.html                 ← Landing page
├── login.html                 ← Login
├── signup.html                ← Signup (student or instructor)
├── student-dashboard.html     ← Student dashboard
├── instructor-dashboard.html  ← Instructor dashboard (courses + live classes)
├── live-classes.html          ← Browse & buy live class tickets
├── css/
│   └── styles.css             ← Shared styles
├── js/
│   ├── supabase-client.js     ← Supabase client + helpers
│   └── ui.js                  ← Toast, nav, formatters
├── vercel.json                ← Vercel routing & headers config
└── supabase-schema.sql        ← Full DB schema (run once in Supabase)
```

---

## Supabase Tables Summary

| Table                | Purpose                                         |
|----------------------|-------------------------------------------------|
| `profiles`           | All users (students & instructors)              |
| `courses`            | Course listings created by instructors          |
| `course_modules`     | Sections inside a course                        |
| `course_lessons`     | Individual video/lesson within a module         |
| `enrollments`        | Student ↔ Course relationships + progress       |
| `live_classes`       | Zoom/Teams live classes by instructors          |
| `live_class_tickets` | Monthly ticket subscriptions by students        |
| `notifications`      | Realtime notifications for students             |
| `reviews`            | Course ratings & comments                       |
| `payments`           | Audit log for all transactions                  |

---

## What's Next (Recommended Features)

- **Payment Gateway:** Integrate PayHere or Stripe for real LKR payments
  - PayHere (local): https://www.payhere.lk — supports cards, bank transfer, FriMi
- **Course Video Player:** Use Mux or Bunny.net for video hosting
- **Email Notifications:** Use Supabase Edge Functions + Resend for transactional emails
- **Course Builder:** Add a lesson upload page for instructors
- **Mobile App:** Convert to a PWA (add `manifest.json` + service worker)

---

## Support

If you run into issues:
- Supabase Docs: https://supabase.com/docs
- Vercel Docs: https://vercel.com/docs
- Supabase Discord: https://discord.supabase.com
