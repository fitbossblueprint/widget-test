# The Three Pillar Blueprint — Build Handoff

**Purpose of this doc:** hand this project to a fresh chat so it can finish the work. It captures the vision, the current state, the exact open decision, the file architecture, and how to verify changes. Read it top to bottom before touching code.

**Last updated:** 2026-07-22

---

## 0. TL;DR — where we are and what's next

- **What it is:** a free lead magnet for **Fit Boss** (founder: Reuben Brooks, @approvedbybrooks), delivered as **one self-contained HTML file** — a scroll-driven "film" that teaches online fitness coaches the 3 pillars (Marketing, Sales, Management) and the 15 systems inside a leak-proof business.
- **The metaphor:** a business is a **faucet**. Money is gold. It flows through a **pipe** with three **joints** (the pillars). Each joint can **leak** (a broken system); you **seal** it. Sealed, the gold runs into the **vault**. Inside the vault are the **15 systems** that keep every joint sealed.
- **Done this session:** (1) smoothed the "enter the vault" motion + revived the interior camera; (2) reframed the 15 systems as **safe-deposit vault drawers** that unlock on scroll; (3) prototyped a new **"vault planet"** presentation for the systems.
- **THE OPEN DECISION (blocks the next big step):** how the 15 systems are presented inside the vault. Two prototypes exist — **Version A (content in orbit)** vs **Version B (opens into a panel)**. The founder/operator (Jared) needs to pick A, B, or a hybrid. Then integrate the chosen planet into the real file.
- **Also outstanding:** only the **5 Marketing** system scenes are wired into the real file. **Sales (5) and Management (5) still need to be built/wired.**

---

## 1. Repo, branch, files

- **GitHub repo:** `fitbossblueprint/widget-test`
- **Working branch:** `claude/motion-repository-lawerq` (develop + push here; do NOT push elsewhere without permission)
- **The real deliverable:** `Three_Pillar_Blueprint.html` (~1.45 MB, at repo root)
- **Prototypes (this session, in `prototypes/`):**
  - `vault-planet_base.html` — scroll-driven planet, base engine
  - `vault-planet_A_content-in-orbit.html` — Version A
  - `vault-planet_B_opens-into-panel.html` — Version B
  - `index-hub.html` — a branded index page linking all artifacts
- **Installed design skill:** `.claude/skills/` contains the **ui-ux-pro-max** plugin (7 skills: ui-ux-pro-max, ui-styling, design, design-system, brand, slides, banner-design). It has GSAP motion presets + a design database; useful for the polish work.
- **Ignore:** `index (1).html` at root is an unrelated leftover "Fit Boss Clock" widget. Not part of this project.

### Commits so far on the branch
```
Reframe the 15 systems as safe-deposit vault drawers
Fix vault entry motion and revive the interior camera
Install ui-ux-pro-max design skill package (7 skills)
Add files via upload           <- original file
```

---

## 2. The file is 100% self-contained

`Three_Pillar_Blueprint.html` works offline. Everything is inlined:
- **Libraries:** GSAP 3.12.5 + ScrollTrigger + Lenis (smooth scroll) — all inlined as `<script>` blocks.
- **Fonts:** `Archivo Black` (display, weight 900) and `Darker Grotesque` (body) as base64 `@font-face`. This is most of the file size.
- **Images/proof:** all base64 (real client screenshots, permitted).
- **Only external call:** one Trainerize YouTube embed (in a scene). Everything else is local.
- **Deploy target:** Jared's Vercel. The Follow-Up tracker download expects `Fit_Boss_Lead_Follow_Up_Tracker.xlsx` in the same folder.

---

## 3. Architecture of `Three_Pillar_Blueprint.html`

There are **two camera systems** driving one SVG camera (`#cam` transform, set by `applyCam()`), plus a stack of scroll-scrubbed reveals. Understanding this is essential before integrating the planet.

### 3a. The film (Phase 1) — scroll-scrubbed GSAP timeline
- `.film` is a tall section (currently **860vh**). A `ScrollTrigger` with `scrub:true` drives a paused master timeline `tl` from progress 0→1 over the film's scroll length.
- The timeline animates a `CAM` object `{x,y,s,ax,ay}`; every update calls `applyCam()` which writes the `transform` on the SVG `#cam` group. `CAM.s` is zoom.
- Story beats (search the `<script>` for the numbered comments): faucet handle → gold runs the pipe (`#money` path) → three joints leak (`.spray`) and seal (`.seal`) → gold fills the vault (`#hoard`) → dial spins (`#dial`) → doors open (`#doorLg`/`#doorRg`) → **push through the doorway into black → black beat → swap `#outside`→`#room` → emerge/settle into the vault**.
- **The entry was rebuilt this session** (was a hard `inExpo` cut in ~130px; now a smooth `inOutQuint` push + black hold + `outExpo` emerge). If you re-time it, keep the swap under full black.

### 3b. Phase 2 — continuous camera drift through the vault interior
- After the film hands off, `worldUpdate()` (called on scroll via `queueWorld`) drives `CAM` as a **continuous eased drift** across the reading section: slow push-in + downward travel + gentle lateral sway, so the vault "breathes" behind the copy.
- **Important history:** the ORIGINAL Phase-2 selected `.tblock`/`.node` for camera waypoints and `.fitbox`/`.node .panel` for a room-fade — **those elements no longer exist** (retired), so the camera used to freeze and ghost tables showed. Both were rewritten this session to key off the real `.arrival`/`.scene` content. **If you add/replace the interior, do not reintroduce dead selectors.**

### 3c. The vault backdrop (`#room` SVG)
- A dark vault interior: perspective floor grid, ambient gold radial glow, faint vault rings. The 3 old "ghost plate" mock tables were removed. The drifting camera parallaxes past this.
- `.veil` (fixed black layer) ramps to **0.55** over `.roomhold` so the vault stays faintly visible for depth (was going fully black).

### 3d. The reading content + the safe-deposit drawers
- `main.reading` contains `.arrival` (3 pillar overview `.pcard`s) then **5 `.scene` sections** = Marketing systems 01–05. Each `.scene` has `.sc-eyebrow`, `.sc-name`, `.sc-leak`, `.sc-fix`, and `.sc-stage` (the proof visual: bios, diagrams, KPI graph, copy-paste tools).
- **`buildVaultBoxes()`** runs at load and, for each `.scene`, constructs a **sealed brushed-steel drawer** (number, pillar, embossed name, combination dial, rivets, gold trim, SEALED status) as a header, moves the real content into a `.box-inner`, and hides the now-duplicate `.sc-eyebrow`/`.sc-name`. `vboxUpdate()` (scroll-driven) unlocks each drawer as it reaches the upper third: dial turns, SEALED→OPEN, gold trim brightens, contents rise in.
- **Safety pattern to preserve:** drawers are built at runtime from existing markup, so the embedded proof is never touched, and if the script fails or reduced-motion is on, the raw `.scene` content still renders. Keep that fallback.

### 3e. Scroll-scrubbed reveals (all keyed to scroll position, not `once`)
- `sceneUpdate` (`.sc-rise`/`.arr-rise`), `dgmUpdate` (`[data-dgm]`/`[data-proof]`), `tableUpdate` (`.fitbox`), the KPI line draw (`[data-kpi]`), `assembleUpdate` (open-card rails). All are pumped from a single `requestAnimationFrame` in `queueAssemble` on scroll. When you add interior content, add its update to that loop.

### 3f. Testing flag
- Append **`?jump=1`** to the URL to **disable Lenis** (deterministic scroll for headless testing). The file also honors `prefers-reduced-motion` (skips the film, shows a static end state).

---

## 4. THE OPEN DECISION — how the 15 systems are presented

The safe-deposit drawers are **already in the file** and look good, but Jared wants the interior to feel less like "cards on pills" and more like a living world, inspired by igloo.inc / DotDNA / Fxology (adapted to **gold**, per house rules — never green).

**Agreed direction:** when you enter the vault you arrive at a **gold particle "vault core"** (the money) with the **15 systems orbiting it on 3 rings** (one per pillar, 5 systems each). It is **one continuous scroll** — **no clicking**. You scroll *into the orbit* and are carried **system by system**, flying into each to reveal its **leak → fix → proof**, then back out to the orbit for the next.

Two prototypes were built for the "arrive at a system" moment — **pick one** (or hybrid):

- **Version A — content in orbit** (`prototypes/vault-planet_A_content-in-orbit.html`): the leak/fix/proof assemble in the space *around* the enlarged icon; you never leave the orbit. Most immersive; harder to place real proof in 3D; trickier on mobile.
- **Version B — opens into a panel** (`prototypes/vault-planet_B_opens-into-panel.html`): flying in opens one clean full panel (leak/fix/proof); scroll on and you pull back to the orbit. Simpler, calmer, easiest to keep real proof components intact and mobile-safe.
- **Hybrid idea:** Version A's immersion for hero systems (e.g. the Content Strategy "929 → 3.8M" packaging story), Version B's clean panel for the rest.

**Prior AI's lean:** B for safety/proof/mobile; A for wow. Founder to decide.

### Prototype engine notes (for integration)
- Pure vanilla: a `<canvas>` particle sphere (fibonacci points, rotated + perspective-projected, warm-gold, brighter in front) + DOM icon "chips" positioned each frame along 3 tilted elliptical orbits. A single **focus camera** (`ctx.setTransform` scale about a focus point, plus matching math for the DOM chips) creates the "fly into a system" zoom.
- **Placement that made it smooth:** systems are placed at a continuous `i * 72°` around the orbit (stepping out one ring per pillar), so the camera sweeps evenly icon-to-icon. The focus target is **interpolated** between consecutive systems (do NOT snap at the midpoint — that was the "jump" bug Jared caught).
- Scroll maps to `u`: `[0,0.10]` = fly-in/establish (see the whole orbit), `[0.10,1]` = guided tour of the 15, zoom pulsing in at each station, core glow dimming when a system is open, active icon fully lit, others faded.
- Custom inline SVG icons represent each system (target=Market Clarity, layers=Content Strategy, magnet=Lead Gen, funnel=Pipeline, gauge=Sales KPIs, card=Payments, gear=Tech Setup, box=Delivery, shield=Retention, checklist=Daily Ops, etc.). No external logos.

### Integration plan (recommended)
1. The planet becomes the **arrival moment** inside the vault (replacing/augmenting the flat 3-pillar `.arrival` cards), then flows as **one continuous scroll** through the 15 systems.
2. Wire the **real proof** for each system into the chosen presentation (A around the icon / B into the panel) — reuse the existing `.sc-stage` components where possible.
3. Decide the drawers' fate: either the planet **replaces** the drawer-scroll for the systems, or the drawers become the "inside each system" content the planet flies into. (Leaning: planet replaces the linear drawer stack; keep drawer *styling language* — dial, gold, SEALED/OPEN — on the system nodes for cohesion.)
4. Keep it scroll-scrubbed like the film, so entry → planet → systems is one unbroken camera journey.

---

## 5. The 15 systems (content is real; use exactly)

House rule: **the fix for each is already written — do not rewrite it.** Each system = a leak (the problem) + the fix (one line) + a visual (a diagram where teaching, real client proof where proving).

**Marketing (create demand · get seen)** — *these 5 scenes are LIVE in the file*
1. Market Clarity — leak: picking a niche feels like turning people away · fix: target a past version of yourself · proof: two real coach bios ("I help…")
2. Content Strategy — fix: give content a job, package it · proof: **Jonathan packaging story, 929 → 57.1K → 3.8M views** (strongest proof on the page)
3. Lead Generation — fix: teach a problem, solve with a lead magnet, deliver with ManyChat · flow diagram
4. Audience Growth — fix: study winning formats, pick two · proof: climbing views
5. Content KPIs — fix: track watch time, retention, shares, saves · live self-drawing KPI graph

**Sales (convert demand)** — *NOT yet wired; needs building*
6. Booking Calls — fix: DM everyone who likes/comments/follows · storefront + DM opener phone (copy-paste, 626 chars)
7. Pipeline — fix: track every lead at each stage
8. Closing — fix: one process, sell transformations
9. Follow-Up — fix: track every convo, give yourself a reason
10. Sales KPIs — fix: track DMs, show rate, close rate

**Management (keep demand)** — *NOT yet wired; needs building*
11. Payment Systems — fix: Stripe auto-renewal · stacked payment notifications + wins
12. Tech Setup — fix: three tools that talk to each other
13. Client Delivery — fix: same steps, same result, every client
14. Retention — proof: Vanessa's first 6-figure month
15. Daily Operations — fix: the same short list every day

**Copy-paste bonuses (real, working, verified byte counts):** AI prompt (Market Clarity, 914 chars), DM openers (Booking Calls, 626 chars), payment message (Payment Systems, 246 chars).

**Real client wins (permitted, real numbers):** Emma 58K first viral; Jesse first close Tier 1; Vanessa 6-figure month; Cody $899 PIF; Jazmin $3K; Juliana $2K; Brianna $1,200/mo (2 booked, 2 closed); Henry $5,760 close / $6,310 collected (keep his real photo with the $10K sign). Never invent numbers, faces, or testimonials.

---

## 6. House rules (non-negotiable)

- **Gold** = money and meaning only, never decoration. Colors: black bg, off-white linework, gold accent (`--gold:#D2B975`). **No green** (ignore the reference sites' green — brand is gold).
- No em dashes. No "it's not X, it's Y." Fifth-grade reading level.
- Real tool names only (ManyChat, Stripe, Trainerize). No "an app."
- No invented stats/faces/testimonials. Real permitted proof or clearly-illustrative diagrams.
- Reuben's only title is **Founder**.
- Fonts: Archivo Black 900 (display), Darker Grotesque (body).

---

## 7. How to verify changes (this environment)

The file is a scroll-driven experience — **you must drive it in a real browser to judge it.** Chromium + Playwright are available.

- **Playwright:** global at `/opt/node22/lib/node_modules` — run node with `NODE_PATH=/opt/node22/lib/node_modules`. Chromium binary: `/opt/pw-browsers/chromium-1194/chrome-linux/chrome`.
- **Launch pattern:** `chromium.launch({ executablePath: '<chrome>', args: ['--use-gl=swiftshader'] })` (swiftshader for canvas/WebGL).
- **Gotchas that will waste your time if you don't know them:**
  - The page uses **smooth scroll-behavior + Lenis** → a single large `window.scrollTo(bigY)` lands *short* (animates over time). Use **`?jump=1`** (disables Lenis) AND scroll in **small increments**, or set `document.documentElement.style.scrollBehavior='auto'`.
  - **Base64 images must finish decoding before you measure layout**, or the page is short and scroll clamps. `await Promise.all([...document.images].map(i=>i.complete?1:new Promise(r=>{i.onload=i.onerror=r})))` and `await document.fonts.ready`, then `ScrollTrigger.refresh()`.
  - To see a specific element regardless of scroll math, use Playwright **element screenshots** (`locator('.drawer').screenshot()`).
- **Always:** after edits, do a full-scroll smoke test (both directions) and confirm **zero console/page errors**.

---

## 8. Publishing a preview the user can tap

Claude Code's inline preview can't scroll-hijack a 1.4 MB file. To give a tappable full-screen link, publish as an **Artifact**:
- Convert the full document to a **fragment**: keep the `<style>…</style>` block + the inner `<body>` contents; strip `<!doctype>`, `<html>`, `<head>`, `<body>` (the host wraps it).
- Artifact **CSP blocks external requests** — fine here (fonts/images/libs are inlined); the Trainerize YouTube embed simply won't load in the preview (works on Vercel).

### Live artifact links from this session (private to the account)
- Blueprint live preview: `https://claude.ai/code/artifact/aa8c5b00-60f4-40e6-b6e8-2be7a0c00c0c`
- Vault Planet prototype: `https://claude.ai/code/artifact/c41524e5-122c-4f7c-b66a-4280ae3da8d0`
- Version A (content in orbit): `https://claude.ai/code/artifact/688daab9-2888-4740-89dd-af175fecab4f`
- Version B (opens into panel): `https://claude.ai/code/artifact/97cb6406-0c4b-4137-b7db-41ccbbb97976`
- Index hub: `https://claude.ai/code/artifact/7080a713-2c7e-4783-9856-18482d02a0a5`
> A fresh chat can recover any prototype's source with `WebFetch` on its artifact URL, but the source is also committed under `prototypes/`.

---

## 9. Next-step checklist for the new chat

1. **Get the A vs B (or hybrid) decision** from Jared. Open the two prototypes; that choice unblocks everything.
2. **Integrate the chosen "vault planet"** into `Three_Pillar_Blueprint.html` as the vault arrival → one continuous scroll through the 15 systems (see §4 integration plan). Keep it scroll-scrubbed and cohesive with the film's camera.
3. **Wire real proof** into each system node/panel (reuse existing `.sc-stage` components).
4. **Build & wire the Sales (6–10) and Management (11–15) systems** — only Marketing exists today. Match the established scene structure so the planet/camera picks them up automatically.
5. **Polish pass** to the premium standard (spacing, depth, gold-on-black), then full-scroll smoke test on desktop + mobile, zero errors.
6. **Commit to `claude/motion-repository-lawerq`** with clear messages; push with `git push -u origin claude/motion-repository-lawerq`. Do not open a PR unless asked.

---

## 10. Working style that fit this project

- Prototype in isolation → screenshot/verify in headless → get founder reaction → only then integrate into the big file. It kept the 1.4 MB deliverable safe.
- Founder (Jared) is newer to code; explain plainly, show visuals/links rather than describe, and confirm creative direction before large rebuilds.
