# samoconnell.com — Handover

**Written 23 July 2026** from a Claude chat session, so the project can be continued in **Claude Code**.

This **supersedes the earlier handover** of the same date (written before any code existed). The infrastructure facts from that doc are carried forward below and still stand; what's changed is that **the site now exists**.

---

## 1. Where things stand

**A complete, working site has been built. It is not deployed yet.**

| Item | State |
|---|---|
| Code | `index.html` — a single self-contained file, ~850 lines / 57 KB (+ `og.png`, `apple-touch-icon.png`). No build step, no dependencies to install. |
| Repo | **Local git** (initialised 23 Jul 2026; see `git log` for the change record). No GitHub remote yet. |
| Deployment | **None yet.** |
| Domain | `samoconnell.com` — registered, **parked at Porkbun** (A records `207.207.210.50` / `207.207.210.36`; `www` CNAMEs to the same). Parking default `robots.txt` blocks all crawling. |
| Vercel | Team **"Sam's projects"** (`sam-oconnell77-projects`, id `team_uQtafh2byaXdg7AmaEtxC8hI`). Only project: `slotspace`. |
| GitHub | `sam-oconnell77` |

> **Verification note.** The domain/Vercel/GitHub rows were verified earlier on 23 Jul 2026 via DNS and the Vercel connector, but **were not re-verified in the session that built the site**. Re-check before relying on them. Everything in the Code row was verified directly (rendered in a headless browser, DOM and canvas inspected).

---

## 2. What the site is

A personal portfolio for Sam O'Connell — Mechanical & Manufacturing Engineering with Management, Trinity College Dublin (integrated master's, 2024–2029).

**Positioning:** innovation, problem-solving, and shipping — across software *and* hardware. This was an explicit correction during the build: an earlier draft framed him as a "measurement / calibration / evaluation" person and **that framing was rejected**. Don't reintroduce it.

**Design concept — "The Drawing Board."** The site is styled as an engineering drawing / CAD viewport: warm-dark sheet, amber draft line, green for "built / shipped". This is deliberate and ties to the mechanical-engineering degree. Preserve it.

### Sections (in order)

| # | `id` | Nav label | Contents |
|---|---|---|---|
| — | `hero` | — | Photo slot, name, interactive gear train, thesis line, CTAs |
| 01 | `about` | Profile | Bio + "Profile" spec card |
| 02 | `projects` | Work | 5 projects with build-progress meters |
| 03 | `achievements` | Milestones | 5 log entries (awards, results) |
| 04 | `roles` | Roles | 3 leadership cards |
| 05 | `interests` | Off the pitch | 4 interest cards |
| 06 | `contact` | Contact | Headline + 5 links |

---

## 3. Design system

All tokens are CSS custom properties in the `:root` block at the top of `index.html`. **Change colours there, not inline.**

```
--ink       #0E1116   base / casing        --paper      #ECE6DA   primary text
--ink-2     #0A0C10   deepest well         --paper-dim  #B9C0CB   secondary text
--panel     #151A22   raised surface       --muted      #7A8496   labels, captions (retuned 23 Jul 2026 for WCAG AA)
--panel-2   #1B222C   card face            --draft      #F7A93B   ACCENT (amber draw line)
--line      #29323F   hairlines, grid      --built      #57C88A   shipped / built / pass
--line-2    #3A4655   brighter tick
```

**Type:** `Instrument Serif` (display), `Space Grotesk` (body), `JetBrains Mono` (all readouts/labels). Loaded from Google Fonts CDN — the only external dependency in the entire file.

**Convention:** amber (`--draft`) = in progress / active / accent. Green (`--built`) = done, live, shipped, 100%. Meters and status dots follow this.

---

## 4. The JavaScript (6 self-contained IIFEs at the bottom)

| Module | What it does | Notes |
|---|---|---|
| **Gear train** | Canvas `#scope` in the hero. Draws 4 meshing gears as a CAD drawing. | The signature piece. See below. |
| **Custom cursor** | Amber crosshair reticle + trailing ring; ring expands and turns green over interactive elements. | Only active on `(hover:hover) and (pointer:fine)`. Force-hidden on touch. |
| **Boot** | "LOADING DRAWING…" calibration screen on first load. | Skipped on repeat visits via `sessionStorage` key `booted2`. Change the key to force it to show again while testing. |
| **Nav** | Sticky-background on scroll + mobile menu toggle. | |
| **Reveal** | `IntersectionObserver` fade-up on `.reveal` elements. | `.d1`–`.d4` classes add stagger delays. |
| **Rail** | Right-edge scroll-depth indicator with tick marks. | Hidden below 860px (`--rail: 0px`). |

### The gear train — read before touching

The most custom part of the site. It's a hand-rolled canvas drawing, not a library.

- `gears` array holds `{z: toothCount, s: spinDirection}`. Radii are derived from tooth count (equal module), so **gears stay physically meshed at correct ratios** — angular velocity of each gear is scaled by `gears[0].z / gears[n].z`.
- Gears 0–2 sit inline along the horizontal datum. **Gear 3 (the idler) meshes straight up from gear 1** at `idlAng = -Math.PI*0.5`. This position was chosen specifically because earlier placements clipped the top of the frame.
- Cursor X position sets `targetDrive`; `drive` eases toward it. Left of centre = reverse, right = forward, centre = slow idle. Off-canvas returns to a slow default.
- Respects `prefers-reduced-motion` (draws one static frame instead of animating).

**If you change tooth counts or positions, re-verify nothing clips.** Quick check — count lit rows in the canvas and confirm the top-most and bottom-most are inside the frame:

```js
// in devtools, with the hero visible
const c = document.getElementById('scope'), x = c.getContext('2d');
const d = x.getImageData(0,0,c.width,c.height).data;
let top=-1, bot=-1;
for (let y=0; y<c.height; y++) {
  let n=0; for (let px=0; px<c.width; px++) { const i=(y*c.width+px)*4; if (d[i]>60||d[i+1]>60||d[i+2]>60) n++; }
  if (n>6) { if (top<0) top=y; bot=y; }
}
console.log({top, bot, h:c.height});  // want top > 1 and bot < h-1
```

### Known technical debt

**Resolved 24 Jul 2026** — the unused gear-layout variables and the stale idler comment were removed in the cleanup pass. Note the module list above has evolved since this doc was written: the boot IIFE now runs FIRST in the script block (storage-guarded, click/key-skippable, dispatches a resize + honours `location.hash` on finish), the gear loop pauses off-screen, the cursor ring sleeps when converged, a mail-copy module was added, the rail caches its metrics, and a `<noscript>` stylesheet degrades everything script-driven. `git log` is the authoritative change record.

---

## 5. Content status — what's real vs. what isn't

All content came from a questionnaire Sam filled in. **Most is real.** These are the exceptions:

### Needs confirming

- **Award years: CONFIRMED.** Sam confirmed (23 Jul 2026, Claude Code session) that the **Naughton Scholarship** and **Trinity Entrance Exhibition** are both **2024**. (IAMTA 2024 and UCC hackathon 2026 were stated originally.)
- **CV link.** Points to a Google Drive file. **Check the sharing permission is "anyone with the link"** — otherwise visitors hit a request-access wall.

### Written by Claude, not by Sam (fine to rewrite)

- Hero tag **"Always building"** — a neutral placeholder. An earlier draft said "Open to opportunities", which asserted availability he never claimed; don't reinstate without asking.
- Contact headline **"Let's build something good."** and the paragraph under it.
- The **"Currently"** interest card is an explicit placeholder for a line he can update over time.

### Left blank by Sam (questions 22–28)

No answers for: additional interests, what he's currently reading, whether he wants a "now" line, contact-line preference, tone preferences, or **words/claims he wants avoided**. That last one matters — there is no known do-not-say list, so keep claims conservative and confirm new copy with him.

### Pending assets

| Asset | Where it goes |
|---|---|
| **Headshot** | **DONE 25 Jul 2026** — `photo.webp` (348×480 transparent-background, 15 KB, resized from Sam's original which stays untracked via `.gitignore`). The `onerror` fallback ("FIG. 01 — PORTRAIT PENDING") remains as insurance. |
| Scout screenshot | Project 02 — no image slot built yet |
| Guardian screenshot | Project 04 — no image slot built yet |
| Excel certificate | No slot yet; he offered to provide it |

---

## 6. Recommended next step: **don't migrate to a framework yet**

The site is one static file with vanilla JS. That means zero build, zero dependency risk, instant deploys, and nothing to maintain. **Ship it as-is first.** A Vite/React or Astro rewrite would risk the gear-train canvas and the design cohesion for no current benefit.

Reach for a framework only when there's a concrete reason — a blog with many posts, or content that genuinely needs components. If that day comes, **Astro** suits this better than React (static, markdown-native, deploys to Vercel with zero config).

---

## 7. Getting set up

### Repo

```bash
mkdir samoconnell.com && cd samoconnell.com && git init
# copy in index.html and this HANDOVER.md, then:
gh repo create sam-oconnell77/samoconnell.com --public --source=. --push
```

### Local preview

No build needed. Either open `index.html` directly, or:

```bash
python3 -m http.server 8000
# → http://localhost:8000
```

(Use the server rather than `file://` if you're testing `photo.jpg` loading.)

### Claude Code

```bash
npm install -g @anthropic-ai/claude-code   # if not installed
cd samoconnell.com
claude
```

Suggested first prompt:

> Read HANDOVER.md. The site is a single static `index.html` — don't migrate it to a framework. [Then your actual task.]

Docs: https://docs.claude.com/en/docs/claude-code

### Optional: Vercel access inside Claude Code

```bash
claude mcp add --transport http vercel https://mcp.vercel.com
# then run /mcp once inside Claude Code to authenticate
```

---

## 8. Deploying and pointing the domain

1. Import the GitHub repo at **vercel.com/new** under team **"Sam's projects"**. It's a static site — no framework preset, no build command, output = repo root. Pushes to `main` become production, same as SlotSpace.
2. Project → **Settings → Domains** → add `samoconnell.com` and `www.samoconnell.com`. (Open decision: which is canonical — Vercel redirects either way.)
3. In **Porkbun** DNS: delete the parking `207.207.210.x` A records, then add **exactly the A/CNAME values Vercel shows you**. Use the dashboard's values rather than any IP copied from elsewhere — Vercel's have changed over time.
4. Wait for DNS + certificate (minutes to an hour).
5. **Check `robots.txt`** — Porkbun's parking page served one blocking all crawlers. Make sure it's gone once the real site is live, or search engines won't index it.

---

## 9. Guardrails

Things that were deliberate. Change them if Sam wants, but don't change them by accident.

- **Don't reintroduce the "measurement / calibration / evaluation" framing.** Explicitly rejected.
- **Don't add projects he didn't approve.** An earlier draft pre-filled projects he hadn't chosen and he pushed back. The five on the site are exactly the five he listed, in exactly the order he specified (SlotSpace → Scout → Trinity Hall Welfare → Guardian → TOP).
- **Don't name collaborators.** He asked that a collaborator mention be kept out; it was removed entirely.
- **Meters mean build progress** (his choice) — 70 / 60 / 100 / 100 / 20 respectively. Green at 100, amber below.
- **Keep the spec-card tool list to what he listed**: SolidWorks, Python, Excel, AI workflows, plus problem-solving and leadership. Don't pad it.
- **Check mobile before shipping.** Two real overflow bugs were caught and fixed here (`html { overflow-x: hidden }` plus a `<wbr>` in the hero name so it can wrap). Verify at 390px wide after any hero change.
- **Accessibility already handled** — keep it: `prefers-reduced-motion` is respected throughout, focus states are visible, and the custom cursor never activates on touch devices.

---

## 10. Starter CLAUDE.md

Copy into the repo root:

```markdown
# CLAUDE.md — samoconnell.com

Personal website for Sam O'Connell (GitHub: sam-oconnell77).
See HANDOVER.md for full project history, design system, and guardrails.

## Stack
- A single static `index.html`. Vanilla HTML/CSS/JS, no build step, no dependencies.
- Only external dependency: Google Fonts (Instrument Serif, Space Grotesk, JetBrains Mono).
- Do NOT migrate to a framework without asking — see HANDOVER.md §6.

## Local dev
- `python3 -m http.server 8000` then open http://localhost:8000
- No install, no build, no tests.

## Deploy
- Vercel, team "Sam's projects". Static — no build command, output = repo root.
- Push to `main` → production.

## Conventions
- All colours are CSS custom properties in `:root`. Never hardcode hex values inline.
- `--draft` (amber) = in progress / accent. `--built` (green) = done / live / shipped.
- The hero gear train is hand-rolled canvas — read HANDOVER.md §4 before editing it.
- Check the 390px mobile layout before shipping any hero or type change.
- Respect `prefers-reduced-motion`; keep focus states visible.

## Content rules
- Never add a project, claim, or collaborator name Sam hasn't approved.
- Positioning is innovation / problem-solving / shipping — NOT "measurement & calibration".
```

---

## 11. What this handover can't tell you

- Whether the domain, DNS, and Vercel state still match section 1 — re-verify.
- Anything decided in other claude.ai chats; those aren't readable from a Claude Code session. Paste anything important into the repo.
- Sam's answers to questionnaire items 22–28, which he left blank (see §5).
