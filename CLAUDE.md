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
