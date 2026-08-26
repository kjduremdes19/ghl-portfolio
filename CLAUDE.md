# CLAUDE.md — WorkwidKirk GHL Portfolio

## Project Overview

A GoHighLevel-focused portfolio for Kirk Jabe Duremdes, positioning him as a Certified HighLevel
Admin who builds CRM systems, pipelines, and automations. One conversion goal: a booked call,
captured through a GHL form embed wired to his own sub-account.

Separate from `ppc-portfolio.workwidkirk.com`, which sells Amazon PPC work to Amazon sellers. Do not
merge the two — different buyer, different offer.

## Tech Stack

Single self-contained `index.html` — inline `<style>` and `<script>`, no framework, no build step,
no bundler. Opens directly in a browser. Google Fonts is the only external request.

Deploy target: GitHub Pages from `main` / root, custom subdomain `ghl.workwidkirk.com` via a
Namecheap CNAME to `kjduremdes19.github.io.` (same pattern as the PPC portfolio).

## Key Files

| File | What it is |
|---|---|
| `index.html` | The whole site |
| `copy.md` | Source copy, with every claim traced to a real file and `[confirm]` markers on anything unverified |
| `ghl-form-setup.md` | The GoHighLevel builder-side work: form fields, custom CSS, workflow wiring, verification |
| `CNAME` | `ghl.workwidkirk.com` |
| `assets/` | headshot.webp, favicon.png, og-image.png |

## Design tokens

WorkwidKirk palette, defined in `:root`. Deep Blue `#0D1F3C` · navy-deep `#081527` · Emerald
`#2ECC71` · emerald-as-text `#0F6B45` · Cream `#F5F0E8`.

Type: Space Grotesk (display) + Inter (body) + JetBrains Mono (labels and metrics).

## Commands

| Task | Command |
|---|---|
| View | open `index.html` in a browser |
| Screenshot / audit | Playwright — Chrome's `--window-size` clamps at ~485 CSS px, so it cannot render a true 390px viewport |
| Optimise an image | `ffmpeg -i in.png -vf "scale='min(1400,iw)':-1" out.webp` |

## Guardrails

**Hard rules — never:**
- Never invent a metric, a client name, or a testimonial. Every number on this page traces to
  `~/.claude/soul.md`, `~/Resume/case-studies.md`, or a real project folder. There are no HighLevel
  testimonials yet, and the FAQ says so on purpose.
- Never present Kirk as agency-connected. He is an independent practitioner.
- Never label the dental pipeline work as a shipped client build. It is a blueprint.
- Never reproduce the HighLevel or GoHighLevel logo. The certification can be named; the mark cannot
  be used.
- Never reuse the PPC portfolio's form (`3A04WWn7tWtH8dCdM6QD`). Wrong questions for this audience.
- Never set emerald `#2ECC71` as text or as a focus ring on a cream surface — it lands at 1.6:1.
  Use `--emerald-text` `#0F6B45` there.

**Soft rules:**
- Emerald fill belongs to the booking CTA alone. Nothing else on the page gets it.
- The `.formslot` reserves the embed's height only via `:has(iframe)`. When the real embed lands,
  copy the snippet's `data-height` into that rule and re-check it whenever fields change.
- Keep all motion inside the `prefers-reduced-motion` guard.
- Two typefaces plus mono. No third family, and headings never share the body font.

## Verification

Before any deploy: re-run the contrast, tab-order, and 320px reflow checks (all previously passed
with headroom), then the form path end to end per `ghl-form-setup.md` → Verify after deploy.
