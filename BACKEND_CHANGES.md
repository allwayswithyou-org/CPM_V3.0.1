# Care Plan Manager V3 — Backend Changes Required

**For:** CPM engineering team
**Purpose:** The V3 redesign is **front-end only** for the large majority of pages — they are drop-in Jinja templates that preserve every existing route, form field `name`, JS function, and privilege gate. This document lists the **small number of backend changes** needed before specific V3 features run fully.

Nothing here changes the database schema unless explicitly stated. Items are grouped **Required** (a V3 feature won't work without it) vs **Optional** (enhancements V3 is designed for but can ship later).

---

## REQUIRED before launch

### 1. Care Plan Manager — show Active clients only
**What:** The redesigned manager grid is intended to list **only `activity_status = 'Active'`** clients (Suspended / End of Service excluded).
**Why backend:** The grid is **server-paginated**, so this must be applied in the Flask/DB query — doing it in the template alone would break pagination counts and page numbers.
**Change:** Add `WHERE activity_status = 'Active'` (or equivalent filter) to the `clients.client_list` query and the count used for pagination.
**Effort:** ~1 line + the count query.

### 2. Reassessment — "Send Reassessment Email to Assessor"
**What:** V3 makes the Reassessment action consistent with Home Assessment: a **"Send Reassessment Email to Assessor"** button.
**Why backend:** The current `reassessment` route is a notes + save form with **no email send**. This is genuinely new behaviour.
**Change:** Add a route/handler that composes and sends the reassessment email to the assigned assessor (mirror the existing Home Assessment email logic), using the RA email template defined in Settings → Email Template (`ra_email_subject` / `ra_email_body`).
**Effort:** Small — clone the HA email handler.

### 3. New "My Profile & Access" page — route + a few session/template values
**What:** A new read-only page showing the logged-in user's identity, **privilege level**, **tenant**, and **assigned branches**.
**Why backend:** Needs a new route, and some values may not currently be exposed to templates.
**Change:**
- Add a route (e.g. `GET /my-access` → `account.my_profile`) + add the link in the account menu.
- Expose to the template: `user_full_name`, `user_email`, `user_privilege_level`, `user_tenant_id` / tenant name, and **assigned branches list** (this last one is the field most likely not currently in session/context — confirm it's available; if not, add it).
- Optional fields if available: `member_since`, `last_login`, `account_status`.
**Effort:** Small route + confirm/expose branch list.

### 4. Signature Settings tab — wire to real provider sub-templates
**What:** The V3 Settings → Signature tab is a **visual mockup**. Production loads provider config dynamically via `{% include selected_provider.template_path %}` and posts to `/settings/provider-config`.
**Why backend:** The real PandaDoc/DocuSign field names live in provider partials that were not part of the design source, so the mockup's fields are illustrative.
**Change (at deploy):** Keep the existing working signature template's field wiring and `include` mechanism. Apply the V3 visual styling around it; do **not** replace the field bindings with the mockup's. (The global theme already modernises it visually.)
**Effort:** Styling pass only — no new logic, but must use the real partials.

---

## OPTIONAL (V3 is built for these, can ship later)

### 5. Multi-branch filter on Care Plan Manager
**What:** V3 lets a user tick **multiple branches** in the filter (touch-friendly checkboxes).
**Why backend:** Server-side filtering currently expects a single branch.
**Change:** Accept a list for `search_branch` (e.g. `request.args.getlist('search_branch')`) and use `WHERE branch IN (...)`. If skipped, the UI still works but only the first selected branch is honoured.
**Effort:** Small query change.

### 6. User Branch Assignments (Settings → Branches)
**What:** V3 adds a per-user **branch assignment** panel (assign which branches each user can access, with All/Save/Reset/Clear).
**Why backend:** Needs an endpoint to persist a user→branches mapping.
**Change:** Add a save endpoint (e.g. `POST /settings/user-branches`) writing the user/branch assignments. Only needed if you want this management UI live; existing assignment mechanism otherwise unchanged.

---

## NO backend change needed (front-end drop-ins)
These V3 templates preserve all existing routes / field names / JS and are safe drop-ins once the global `cpm-theme.css` is linked:
- Care Plan Manager grid layout, status chips, filters UI (except items 1 & 5 above)
- Client Profile (tabbed) — uses the existing `edit_client_profile` context
- Care Plan view / edit & approval flow — existing markdown editor, version, and signature workflow fields preserved
- Assessment SA/HA pages (`page3`/`page4`) — existing send-email logic preserved
- Onboarding (Add Client + Care Coordinator combined) — existing field names preserved
- Reports (Care Plan Activity / Weekly / System Logs) — existing pagination & filters
- Settings tabs (Users, Branches list, Care Plan Template, Email Template, Tenant)
- Release Notes (view + admin) — existing `version/title/features/improvements/bug_fixes/published` fields + L0 gate
- Login, 404/500, navbar, footer

---

## Global rollout mechanics (front-end)
1. Add `static/css/cpm-theme.css` and link it **once** in `base.html` (before `</head>`, after the inline `<style>`). This modernises every legacy page instantly.
2. Drop the brand assets into `static/images/` (logos SVG + `AWY_login_bg.png`).
3. Replace the page templates with their V3 versions (same filenames).
4. Paste the V3 navbar + footer blocks into `base.html`.
5. **Test on dev/staging first** — CPM is in production.

---

## Summary for planning
| # | Item | Type | Effort |
|---|------|------|--------|
| 1 | Active-only client query | Required | XS |
| 2 | Reassessment email-to-assessor route | Required | S |
| 3 | My Profile & Access route + branch list | Required | S |
| 4 | Signature tab → real provider partials | Required (at deploy) | S (styling) |
| 5 | Multi-branch filter | Optional | XS |
| 6 | User branch-assignment save endpoint | Optional | S |

Everything else is front-end and requires no backend work.
