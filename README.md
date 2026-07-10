# Care Plan Manager — V3.0.1 UI Preview.

A clickable, front-end-only design prototype for **user testing**, ahead of the V3.0.0 production launch. Sample data only — not connected to any backend or real client data.

**Start page:** `index.html` (a launcher linking every screen).

---

## What's in this pack

```
v3/
├── index.html                     ← testing launcher (start here)
├── login_V3.html
├── care_plan_search_V3.html       ← Care Plan Manager
├── client_profile_V3.html         ← Profile / Intake / Assessments / Care Plan / Support Docs / Reassessment (tabs)
├── assessment_pages_V3.html
├── onboarding_V3.html
├── document_view_V3.html
├── report_pages_V3.html
├── settings_V3.html
├── user_profile_V3.html
├── release_notes_V3.html
├── admin_release_notes_V3.html
├── error_pages_V3.html
└── assets/
    ├── logos/ALWAYS_logo_horiz_RGB_NEG.svg
    ├── logos/ALWAYS_logo_horiz_RGB_POS.svg
    └── awy-brand-bg.png
```

All links and asset paths are relative, so the whole folder works wherever it's hosted (GitHub Pages, Netlify, or opened locally).

---

## Deploy to GitHub Pages (free, ~5 minutes)

**Option A — web upload (no command line):**
1. Go to https://github.com/new and create a repo, e.g. `cpm-v3-preview`. Make it **Public** (Pages is free for public repos).
2. On the repo page, click **Add file → Upload files**.
3. Drag in **the contents of this `v3/` folder** (the HTML files, `index.html`, and the `assets/` folder) — so `index.html` sits at the repo root. Commit.
4. Go to **Settings → Pages**.
5. Under **Build and deployment → Source**, choose **Deploy from a branch**; set Branch = `main`, Folder = `/ (root)`. Save.
6. Wait ~1 minute. Your preview is live at:
   `https://<your-username>.github.io/cpm-v3-preview/`
7. Share that URL with testers.

**Option B — git command line:**
```bash
cd v3
git init && git add . && git commit -m "V3.0.0 UI preview"
git branch -M main
git remote add origin https://github.com/<your-username>/cpm-v3-preview.git
git push -u origin main
```
Then enable Pages as in steps 4–6 above.

> **Tip:** keep the repo on a `preview`/`design` branch separate from your live CPM codebase. This pack is **front-end mockups only** — do not merge it into the production app.

---

## What testers should know
- It's a **visual prototype**: click through screens, try search/filters/tabs/toggles. Nothing saves; no real data.
- Suggested path: **Care Plan Manager → click a client → Profile → Care Plan (View & approvals) / Assessments / Documents**.
- Role-gated features (e.g. Release Notes admin) include a small demo toggle to preview both states.

---

## Path to V3.0.0 production
These mockups are the design reference. Production rollout is the **deploy package** (the `deploy/` folder from the build): `cpm-theme.css` + the wired Jinja templates that preserve all routes, field names and logic. Two items still need backend-phase wiring:
1. **Assessment pages** (`page3`/`page4` are front-end-safe; the Reassessment "email to assessor" needs a new backend route).
2. **Signature settings tab** — wire to the real provider sub-templates / field names.
