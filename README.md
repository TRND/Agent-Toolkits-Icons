# Agent-Toolkits-Icons

Public mirror of the **TRND Agent-Toolkits plugin logos** — one `logo.png` +
`logo.svg` pair per toolkit pack, served at stable `https://` URLs for
marketplace icon rendering.

```
toolkits/<pack-id>/assets/logo.png   # 256×256 RGB (no alpha) — marketplace tile
toolkits/<pack-id>/assets/logo.svg   # vector source twin of the tile
```

## Why this repository exists

It answers a question that gets asked: *why is there a separate repo just for
icons?* Because the consuming renderer leaves no alternative:

1. **ZCode's plugin-card renderer draws an icon only when the marketplace
   entry's `icon` value is an `https://` URL.** This is verifiable in the
   host's renderer code — anything else (a relative path, a missing field,
   a non-https URL) falls back to a generic placeholder glyph. For months
   every TRND pack card showed that placeholder even though every pack
   carried perfectly good logo files.
2. **The canonical repository, [TRND/Agent-Toolkits], is private.** An
   unauthenticated image load of
   `raw.githubusercontent.com/TRND/Agent-Toolkits/...` returns 404 — a
   webview `<img>` tag carries no GitHub credentials, so the private repo
   can never serve its own assets to the marketplace UI.
3. **The alternatives were all worse:**
   - Making the main repository public → exposes all internal tooling. Rejected.
   - Third-party image hosts → uncontrolled uptime, link rot, brand assets
     on infrastructure we do not operate. Rejected.
   - GitHub Pages inside the private repo → the TRND organization has Pages
     site creation disabled at the org-policy level (API returns
     `422: GitHub organization administrators disabled Pages creation`),
     and publishing the repo root would leak the repository anyway.
     Unavailable as of 2026-08-28.
   - **A dedicated public repository containing logos only** → brand marks
     are public-by-design (marketplace UIs display them), it contains no
     code and no secrets, and it is trivially deletable. Chosen.

## What this repository is NOT

**This is not the source of truth.** The canonical art lives in
[TRND/Agent-Toolkits] at `toolkits/<pack>/assets/`, generated from the glyph
registry (`scripts/lib/pack-logo-family.mjs`) by a deterministic generator.
Every file here is a **publish artifact** — do not edit anything in this
repository by hand; the next sync overwrites it.

## How it stays correct

- **Publish:** `node scripts/sync-plugin-icons.mjs` (in Agent-Toolkits) copies
  `toolkits/*/assets/{logo.png,logo.svg}` here and pushes.
- **Verify:** `node scripts/check-logo-family.mjs --remote` (in Agent-Toolkits)
  byte-compares every mirrored file against the canonical copy and fails on
  drift; the offline form of the same gate runs in CI and enforces that every
  marketplace entry's `icon` points at this repository's URL.
- **Regenerate:** changing a logo means editing the glyph registry and running
  `node scripts/gen-pack-logos.mjs --pack <id>` — never hand-editing PNGs
  (hand-rasterization is what broke these logos for ten-plus rounds; the
  renderer drops `objectBoundingBox` gradients on straight lines, and alpha
  channel tiles render as black/transparent blobs).

## Historical context

Between the marketplace's launch and 2026-08-28, plugin logos broke in more
than ten successive fix rounds. Root causes (all now gated against in the main
repository): no `icon` wiring the renderer could consume, alpha-channel tiles,
gradient-stroke rasterizer defects, unscaled glyph geometry, and the absence
of any committed art source. PR [TRND/Agent-Toolkits#954] ended that era; this
mirror is the publishing half of that system.

## Branding & licensing

Marks are TRND brand assets or TRND-authored geometric marks. Repo layout
mirrors the main repository for a 1:1 path mapping. Contact the TRND org
owners for usage questions.

[TRND/Agent-Toolkits]: https://github.com/TRND/Agent-Toolkits
[TRND/Agent-Toolkits#954]: https://github.com/TRND/Agent-Toolkits/pull/954
