# Driven Plastics — Proposal Generator

## What this is
A single-file HTML/JS tool that generates printable pavement preservation proposals for Driven Plastics sales reps. The entire app lives in `index.html` — no build step, no dependencies beyond CDN scripts (Mapbox GL, MapboxDraw, Turf.js).

## How to work on it
Open `index.html` directly in a browser. All edits are to that one file. Commit to GitHub when a meaningful chunk of work is done.

## Services covered
- **Delta Mist** — spray-on asphalt rejuvenator, priced per SY (editable rate, default $1.39)
- **TDRI** — subsurface moisture scan, priced per lane mile (auto-calc, manual, or flat day rate)
- **RiaaS** — AI road condition scan, same pricing modes as TDRI
- **Test Coring** — optional add-on, $10k/section
- **rPF** — recycled polymer future paving plan, TBD pricing

## Architecture notes
- **State** lives in JS variables: `deltaMistOn`, `tdriOn`, `riaasOn`, `coringOn`, `rpfOn`, `tdriMode`, `riaasMode`, `siteMaps[]`, `serviceMap`, `RESPONSIBILITY_ITEMS[]`
- **`update()`** is the single re-render trigger — call it after any state change
- **`renderSiteMaps()`** rebuilds the entire left-panel site map + sections UI; `renderSections()` is an alias for it
- **`renderModalSidebar()`** rebuilds the expanded map modal's right panel — must be called separately from `renderSiteMaps()`
- **`syncToggles()`** keeps the Section 1 quick-toggle mirrors in sync with the detail-section toggles
- Polygon geometry is stored in `siteMaps[i].sections[j].geometry` (GeoJSON) — MapboxDraw is only used for the draw interaction, colored fills are rendered via a separate GeoJSON source
- Snapshots are captured from the modal map canvas as JPEG data URLs and stored in `siteMaps[i].snapshot`

## Proposal rendering
- **`renderProposal()`** builds the full proposal HTML and injects it into `#proposal-doc`
- The Project Overview section checks `#project-overview` textarea — if it has content it overrides both auto paragraphs (split on blank lines into separate `<p>` tags); `window._lastOverview` and `window._lastPara2` cache the auto-generated text for pre-filling the editor
- Second paragraph (services summary) is auto-built from active services; TDRI+RiaaS combined as "a TDRI and RiaaS scan" when both on, with "as well as" prefix only when Delta Mist is also on

## Key decisions
- No framework — plain JS, single file, works offline after first CDN load
- MapboxDraw polygon body drag is disabled (only vertex drag allowed) — intentional, prevents accidental moves
- `renderSections()` must stay as an alias for `renderSiteMaps()` — many functions call it
- File was versioned as `_12` during development; renamed to `index.html` going forward, git handles history
- All service toggles default OFF

## Repo
https://github.com/Bucky303/driven-plastics-proposal
