# EQLocate 3 — redesign report

Author: **Ali Attia**

## Deliverables
```
EQLocate_project/
├── original_backup/EQLocate_2.7.1_original.html   untouched original (single 304 KB file)
├── EQLocate_3_redesign/                          the new application — open index.html
│   ├── index.html                                structure (all 150+ original element IDs kept)
│   ├── assets/css/eqlocate.css                   design system (light "Paper" + "Darkroom" themes)
│   ├── assets/js/core.js                         numerical core — verbatim from 2.7.1
│   ├── assets/js/figkit.js                       SVG/PNG/ZIP figure kit — verbatim from 2.7.1
│   ├── assets/js/app.js                          application logic (2.7.1 + theming, offline map)
│   ├── assets/js/shell.js                        new interface layer (tabs, rail, plate, keys, toasts)
│   ├── assets/vendor/leaflet/                    Leaflet 1.9.4, local
│   ├── assets/data/world50m.js                   offline world map (Natural Earth 1:50m)
│   ├── assets/fonts/                             subset WOFF2 fonts (Fraunces, Archivo, Plex Mono) + OFL licences
│   └── samples/synthetic_event/                  synthetic 7-station SAC test event (labelled synthetic)
└── REPORT.md
```

## Media and external resources
- **Videos: none.** The supplied application (EQLocate 2.7.1) contains no video elements, players or video links. The folder `C:\Ali\wafaa_homepage04` is on a Windows PC that was not connected to this cloud session, so its contents could not be read; no video was downloaded or invented.
- **Removed runtime dependencies:** `unpkg.com` (Leaflet JS + CSS) now bundled locally. Web fonts are local subset files (≈115 KB). The app makes **zero network requests** on load (verified).
- **Map:** new default basemap is an offline vector map (Natural Earth land + borders + zoom-aware graticule). Esri imagery and OpenTopoMap remain as *optional* online layers — their tiles can't be bundled (licence and size), so they load only if selected.

## Revision 4 — publication maps with GMT (Case study & figures)
- **Why a package:** GMT is a command-line program and cannot run inside a browser. The Case study workspace
  therefore exports a GMT package (button "Download GMT map package"; also inside "Download all" under gmt/).
- **Package contents:** all data tables, map regions computed by EQLocate and the script make_maps.sh (GMT ≥ 6.1).
- **Figures (PDF + 600-dpi PNG):**
  - **G1 location overview:** map with stations, every solution and a locator inset, N–S and E–W depth sections
    aligned to the map axes, and one legend below all panels.
  - **G2 three-circle construction:** S−P circles, radical axes, radical centre and the sphere and joint solutions.
  - **G3 epicentre uncertainty:** (a) Monte Carlo clouds with 1σ and 95 % ellipses; (b) Bayesian posterior coloured
    by depth; (c) jackknife epicentres and velocity sensitivity coloured by Vp. Each panel has its key directly
    beneath it.
- **Backgrounds:**
  - satellite imagery (@earth_day) shaded with SRTM relief (default);
  - SRTM 1″ shaded relief (BG=relief);
  - your own high-resolution GeoTIFF, e.g. Sentinel-2 at 10 m (BG=image IMG=…);
  - coastlines only (BG=plain, works offline).
  If a download fails, the script falls back automatically: relief, then coastlines.
- **Layout safeguards:**
  - Regions are computed so every symbol is inside the frame.
  - The inset and scale bar go to the emptiest corners.
  - Regions are grown slightly so that no annotated tick sits at a corner, so axis labels never collide.
  - Legends and colour bars sit outside the map frames.
  - There is one house style: fonts, pens and symbols.
  - Every axis uses exact custom annotations, which avoids a GMT 6.5 formatting bug that drops a zero
    (36.075 shown as 36.75).
  - Each run uses a private GMT session, so stale sessions cannot corrupt a figure.
- **Tested here with GMT 6.5:**
  - coastline background and a locally generated stand-in GeoTIFF;
  - fallback chain with no network.
  The GMT data server is blocked in this environment, so @earth_day / @earth_relief could not be downloaded or
  rendered here. That path uses the same commands as the tested GeoTIFF path.
  Example output (coastline background): gmt_example_output/.

## Revision 3 — application shell with independent workspaces
- **Fixed left sidebar** (Home, then Data: Data & audit 1, Picking 2; Analysis: Wadati 4, Velocity 5, Three circles 6,
  Joint LS 7, Bayesian 8; Output: Case study 9). Numbers keep the original section numbers, so every message that
  says "section 7" still points to the right place. Each button shows a status dot (ready / done) and badges for
  failed pick checks and figure count; the sidebar collapses to icons (button, or automatically below 1180 px).
- **One workspace at a time:** each has a scrolling controls panel on the left and only its own views on the right
  (Data → pick audit; Picking → seismogram; Wadati → diagram; Velocity → Wadati diagram; Three circles → map;
  Joint LS → map + depth scan + Wadati; Bayesian → depth posterior + map; Case study → figures). The plot/map views
  are shared elements moved into the open workspace, so nothing is duplicated and all state is kept.
- **Desktop layout:** the app fills the window with no page scrolling; a top bar shows the workspace title and a live
  results readout (stations, Vp/Vs, epicentre, depth, origin time, offset to reference); a status bar shows the
  session, timings and the current pick.
- **Home page:** what the software does, a live session summary, the typical workflow, privacy/outputs, and eight
  workspace tiles (with status) that open each workspace.
- **Navigation:** sidebar, Home tiles, keys 1–9 and H, browser address (#joint etc.) with back/forward support.
- Decorations removed (background pattern, banner, trace strip); compact controls and tables kept.
- Tested by driving every function through the sidebar: identical outputs to 2.7.1, 16 figures, ZIP/session export,
  zero page scroll at 1600×900, no horizontal overflow on a phone, works from file:// and http://.

## Revision 2 — reference-style redesign, compact layout, Google Satellite
- **Visual language** (after the supplied reference, without copying its brand): night-ink index sidebar with numbered
  eight-point-star badges (filled gold = step done, gold outline = ready), cream paper with a faint star lattice,
  Fraunces serif headings with brick-red italics, gold rules, notched brick-red primary buttons, underlined link actions.
- **Compact:** controls 28 px high, tighter step headers and tables, a slim title strip instead of a hero, the index
  collapses to a 64 px badge column below 1240 px and to a badge strip on phones. Canvases render at up to 3× device
  pixels for sharp traces and axes.
- **Google Satellite basemap** via Google's official Map Tiles API (session token + your own API key, entered under
  "Google Satellite key" below the map, stored only in your browser, sent only to tile.googleapis.com). It's the
  default once a key is saved; Google's live imagery attribution is shown. Options: Google Satellite, Google Satellite +
  labels, Esri World Imagery (no key, default otherwise), OpenTopoMap, offline coastlines. If online tiles can't load,
  the map switches to the offline coastlines automatically.
  The unofficial `mt*.google.com` tile URLs were deliberately not used: Google's terms don't allow them.
- Tested: Google path with mocked endpoints (session request carries the key, tiles and attribution load), fallback to
  offline, full workflow parity with 2.7.1 (identical outputs), file:// and http://, phone width without overflow.

## Redesign (revision 1)
Concept: a **field instrument** — a seismograph logbook rather than a dashboard.
- Paper datasheet palette with ink-black controls; one signal colour per seismic meaning, used consistently in canvas, map and legend: P vermilion, S verdigris, three-circle ochre, joint LS ultramarine, posterior violet.
- Archivo (variable width) in condensed caps for headings, IBM Plex Mono for every number; Greek supplements so σ, Δ, χ, κ render consistently.
- Masthead ends in a recorded "drum" trace; brand mark is a trace through a target.
- Layout: numbered logbook (01–09) on the left, sticky instrument viewport on the right with a **solution plate** (network, Vp/Vs, epicentre, depth, origin, offset to reference) and tabs: Seismogram · Wadati/depth/map · Pick audit · Figures.
- **Workflow rail** shows each step's status (idle / ready / done) and jumps to it.
- Long method text moved into quiet disclosures (all wording kept). Blocking `alert()` boxes replaced by toasts.
- Drag-and-drop SAC loading (loads on selection), keyboard shortcuts (P/S, [ ], 0, 1–4, ?), Paper/Darkroom/Auto theme, responsive down to phone width, print stylesheet.

## Size
| | original | redesign |
|---|---|---|
| app code | 304 KB (one file) | 360 KB HTML/CSS/JS (split, readable) |
| runtime downloads | Leaflet from unpkg + map tiles | none |
| total app | — | 1.1 MB incl. Leaflet, fonts, 404 KB offline world map; +0.2 MB sample data |

## Testing (headless Chromium, Playwright)
- Full workflow on the synthetic event, original vs redesign: load 21 SAC files, Wadati, Vp estimate, three-circle + spheres, fixed-point check, joint LS + Monte Carlo, velocity sensitivity, Bayesian sampling, manual pick + restore header picks, case study (16 figures), ZIP and session export.
- **Every numerical output is identical to the original**, character for character. The solver recovers the synthetic truth (36.105°N 129.312°E, 8.0 km → 36.1053°N 129.3111°E, 8.10 km).
- 0 console errors, 0 failed requests, 0 external hosts — over http:// and when opened straight from disk (file://).
- Phone (Pixel 7): no horizontal scroll, map and canvases size correctly. Light and dark themes checked visually.
- Fixed during QA: dark-mode masthead inversion, overlapping time-axis labels, dense map graticule labels, sticky rail under the masthead, font preloads that failed under file://.

## Not changed
- The numerical core and figure generation (core.js, figkit.js) are byte-identical to 2.7.1. Exported files record version 3.0.0.
