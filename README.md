# Household Skills Board

A single-page tracker for the practical skills kids need before they leave home —
the ones nobody formally teaches. Age-gated, per-child, signed off when they can
actually do the thing rather than when they've heard about it.

164 skills across 10 areas: kitchen, house repair, vehicles, money and taxes,
shop tools, safety and health, paperwork, digital security, outdoors, and
communication.

## Design rules

Two rules shaped the catalog:

1. **If a kid picks it up by osmosis, it isn't on the board.** No bedmaking, no
   shoelaces, no handshakes. Every entry is something that has to be deliberately
   taught or it simply doesn't happen.
2. **Every skill has a testable bar.** Not "know where the shutoffs are" but
   "can walk you to all three in the dark." The sign-off criterion is the skill.

## Using it

- Add each child with a birthdate. Age gates key off that, so the board reveals
  itself over time instead of showing a wall of things nobody's ready for.
- Click a cell to cycle: **not started → working on it → signed off**.
- Cells outline in red when a kid is 2+ years past a skill's start age and hasn't
  begun. That's the only nagging it does.
- Hover a skill row to **hide** it. Hidden skills drop out of the counts,
  progress bars, and queues. The **Hidden** button brings them back.
- **Add skill** appends your own to any area.

## Data

Everything lives in the browser's `localStorage`, on the device, in the clear.
Nothing is transmitted and there is no backend — this repo is static files only.

That means **storage is per-device and per-origin**. The phone and the laptop are
separate boards, and data entered on a different host won't follow the site.
Use **Export** to write a JSON backup and **Import** to load it. That file is
your sync mechanism and your backup; keep it somewhere you actually back up.

`.gitignore` excludes `*.json` so an exported board can't get committed by accident.

## Deploying

Pushing to `main` triggers `.github/workflows/pages.yml`, which publishes the repo
root to GitHub Pages and stamps the service worker cache with the commit SHA so
clients pick up changes instead of serving a stale copy.

One-time setup: **Settings → Pages → Source → GitHub Actions**.

Note: on GitHub Free, Pages only works from a **public** repo. Private-repo Pages
needs Pro or above. Nothing family-specific lives in the repo, so public is fine —
but if you'd rather keep it private, either upgrade or serve it another way (see below).

## Running it without GitHub

It's one self-contained HTML file with no build step and no dependencies:

```bash
# straight from disk
open index.html

# or over http, which is what the service worker and install prompt want
python3 -m http.server 8000
```

## Offline and install

`sw.js` registers over HTTPS and caches the app shell network-first — deploys land
immediately, and the board still opens with no signal. On a phone, **Add to Home
Screen** gives you a standalone app icon.

## Editing the catalog

The skill list is a plain array near the top of the `<script>` block in
`index.html`. Each entry is:

```js
[age, "Skill name", "What counts as done"]
```

Add, cut, or re-age freely. Skill IDs are derived from position within a domain,
so **inserting a skill mid-list will shift the IDs below it** and detach existing
sign-offs from their rows. Append to the end of a domain instead, or use the
in-app **Add skill** button, which mints a stable random ID.
