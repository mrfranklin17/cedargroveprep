# CLAUDE.md — Cedar Grove Board Interview Prep

Project memory for Claude Code. Read this before editing anything.

## What this is
A **capacity-interview prep tool** for the Cedar Grove Schools founding board, preparing for the **Washington State Charter School Commission** capacity interview. It is a static, installable PWA — no backend, no database, no user accounts. Per-device progress only.

Audience: five founding board members prepping on their phones. The tool's job is to make each member able to speak credibly and consistently about the submitted charter application — especially their own domain — so the board doesn't defer every substantive question to the CEO.

## Stack & deploy
- Plain **vanilla HTML/CSS/JS** in a single `index.html`, plus PWA plumbing. **No framework, no build step, no dependencies.** Do not introduce React/Next/bundlers/npm packages unless explicitly asked.
- Hosted on **GitHub → Vercel**. Vercel auto-deploys on push to `main` (Framework Preset: **Other**, no build command, root output). A push is a deploy.
- After shipping a change, verify the live HTTPS URL: load once, then airplane-mode + reopen from home screen to confirm the service worker still serves it.

## File structure (all flat at repo root — do not move into folders)
- `index.html` — the entire app (markup, styles, data, logic).
- `manifest.webmanifest` — PWA manifest.
- `sw.js` — service worker (network-first w/ offline cache fallback). **Must stay at root** for its `./` scope.
- `icon-192.png`, `icon-512.png`, `icon-180.png` — home-screen icons (three sage firs over a pale-blue book on forest green). Regenerate from the emblem SVG if the mark changes.

## Where the content lives
All content is in three JS structures at the top of the `<script>` in `index.html`:
- `MEMBERS` — board roster keyed by id (`pettey`, `wiegner`, `franklin`, `wright`, `dunn`); each has `short`, `name`, `role`, `owns`, `coi`. `ORDER` sets display order.
- `DOMAINS` — topics keyed by id (`MISSION`, `MODEL`, `OUTCOMES`, `STUDENTS`, `GOV`, `COI`, `FIN`, `ENROLL`, `FACILITY`, `COMMUNITY`, `RISK`); each has `code`, `name`, `lens`.
- `Q` — the question bank. Each entry:
  ```js
  { d: "FIN",                 // domain key
    core: true,               // optional; true = every board member must know it (tagged "All")
    owners: ["franklin"],     // member ids, owners[0] is the lead (bold chip); ["all"] = everyone
    q: "the question",
    points: ["talking point (light inline HTML like <b> ok)", ...],
    coach: "optional prep coaching note",   // renders as "Coach's note"
    cite: "Prompt 3.4 — Revenue Overview" }  // where in the application this traces to
  ```
To add/edit questions, edit the `Q` array. Nothing else needs touching.

## Content rules (non-negotiable)
- **Source of truth is the submitted RFP application** ("Cedar Grove RFP - Information Sheet & Prompts"). Every talking point must trace to it — that's what `cite` is for.
- Where the Notice of Intent and the RFP disagree, **the RFP Information Sheet wins.** Canonical numbers: enrollment **230 in Year 1 (140 grade 6 + 90 grade 9) → 780 at capacity by Year 5 (FY2032)**; opens **Fall 2027**.
- Officer slate: **Pettey** CEO & Founding Principal (non-voting) · **Wiegner** Board Chair · **Franklin** Board Treasurer · **Wright** Board Secretary · **Dunn** Board Member.
- **Do not fabricate.** If a claim isn't supported by the application, flag it — don't invent a figure or attribution.
- **Do not reintroduce a "Consistency Watch / Get Your Story Straight" section.** It was intentionally removed. Genuine interview coaching lives in per-question `coach` notes only.
- Content/strategy work (drafting new talking points from the RFP, "what would an evaluator push on") is better done with the source docs in hand than from the repo alone.

## Design (brand-locked)
- Palette comes from the school logos. Colors **only** via the CSS variables in `:root` — never hard-code hex elsewhere:
  `--forest #0F3B2C` · `--green #2E7D46` · `--green-d #256A3B` · `--sage #9CBB8A` · `--sage-d #7C9C6B` · `--sky #C6DAE2` · `--sky-tint #EBF3F6` · `--sky-line #C3D6DE` · `--paper #F6F8F3` · `--card #FFFFFF` · `--ink #1B2A20` · `--ink-soft #56655A` · `--mute #9AA79C` · `--line #DCE3D6`.
- Fonts: **Newsreader** (display/serif), **Inter** (UI), **JetBrains Mono** (labels/codes).
- Identity: three-firs-over-a-book emblem; the owl is the athletics mark and is not used here.
- **Mobile-first app shell**, not a webpage: fixed forest app bar (top, safe-area inset) → scrolling `.screen` → bottom `.tabbar` (Prep / My Seat / Progress). Prep drills topic list → topic detail with a back arrow. **Do not add a marketing hero.** Keep it feeling like an app.

## PWA & state
- `sw.js` is **network-first** (freshest when online, cached when offline). When you change any cached file, **bump `CACHE` (`cg-prep-v1` → `-v2`, …)** or phones may serve stale content.
- Progress persists in `localStorage` (`cg_seat`, `cg_rate_<idx>`), wrapped in try/catch so it works on Vercel and degrades gracefully in sandboxed previews. Keep that pattern — don't use it unguarded.
- No analytics, trackers, or third-party scripts. This is a real school's real application; treat it as internal.

## Working style
- Small, atomic commits with clear messages.
- The app logic is one script; after edits, sanity-check it (e.g., `node --check` on the extracted script) before committing.
- If asked for a timed mock-interview mode, it was scoped but deliberately not built — confirm before adding.
