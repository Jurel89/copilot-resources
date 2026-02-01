# Documentation assets

This folder stores **visual assets used by the Markdown documentation**.

## Structure

- `docs/assets/images/` — static images (banners, icons)
- `docs/assets/diagrams/` — exported diagrams (prefer SVG for crisp rendering)

## Conventions

- Prefer **SVG** for diagrams (renders well on GitHub, scales cleanly).
- Keep filenames **lowercase kebab-case**.
- If you introduce a diagram source format (e.g., DrawIO/PlantUML), keep the exported SVG next to it:

  - `docs/assets/diagrams/thing.drawio`
  - `docs/assets/diagrams/thing.svg`

## Where these are used

- Project landing page: `README.md`
- Docs index: `docs/README.md`
