# CAS-NGS Core Suite — One Plugin, Whole Site

A single zero-build WordPress plugin carrying everything:

| Component | What it is | Editable? |
| --- | --- | --- |
| **CAS-NGS Header** (`cas-ngs/header`) | Floating glass command-bar nav: spring-proximity dock (grow + drop-down), dropdowns, mobile drawer, ambient WebGL motes | ✅ fully — icon, wordmark, tabs, dropdowns, CTA |
| **Sylva Footer** (`cas-ngs/footer`) | Light-mode footer: waving DNA strands, nanopore sequencing lane, link cards, live bases counter | ✅ click-to-edit text + links |
| **Biotech blocks** (Acts 0–4, DNA background, pipeline hero) | Your blocks from the `cas-ngs-biotech-blocks` repo, registered **untouched** from `blocks/` | ✅ exactly as before |

```
cas-ngs-header-footer/          ← zip this folder = your one plugin
├── cas-ngs-header-footer.php   suite bootstrap (blocks, patterns, loader, debug)
├── block.js                    header + footer editor UI (no build step)
├── includes/
│   └── cas-ngs-biotech-blocks.php  integrated biotech bootstrap (no auto-apply)
├── assets/
│   ├── header-footer.css       dock + footer chrome, earth palette tokens
│   ├── header-footer.js        proximity springs, dropdowns, drawer, WebGL,
│   │                           strands + nanopore lane engine
│   ├── css/biotech-blocks.css      ← COPY FROM REPO (step 3)
│   ├── js/biotech-blocks-editor.js ← COPY FROM REPO (step 3)
│   ├── js/biotech-blocks-engine.js ← COPY FROM REPO (step 3)
│   └── models/dna.glb              ← COPY FROM REPO (step 3)
├── blocks/                     biotech block folders (block.json + render.php)
│   ├── act1-hero-sequencer/    act2-bento-grid/    act3-process-timeline/
│   ├── act4-cta-banner/        dna-background/     header-top-dock/
│   └── interactive-pipeline-hero/
├── snippets/header-markup.html static markup sample (non-WP wiring)
└── README.md                   this guide
```

## 1 · Assemble, zip & install

The block manifests (`block.json`) and render templates (`render.php`) for
all seven biotech blocks are already inside `blocks/`, ported untouched
from your repo.

## 2 · Zip & install

1. **Zip the `cas-ngs-header-footer` folder itself** — the plugin file
   (`cas-ngs-header-footer.php`) sits at the folder root, so WordPress
   accepts the zip as-is (right-click the folder → Compress/Zip; do not
   zip the *parent* directory).
2. WordPress admin → **Plugins → Add New → Upload Plugin** → upload the
   zip → activate **CAS-NGS Core Suite**.
3. Open any wp-admin page with `?cas-debug=1` appended (e.g.
   `wp-admin/index.php?cas-debug=1`). The notice lists every block found
   under `blocks/`, its registration status, and **exactly which asset
   files are still missing** (if any).

**Activation changes nothing on the front end.** Every component is a
block you place yourself — nothing is injected anywhere automatically.
(The old plugin's auto-injected dock and DNA background were removed in
this integration; the dock renders only where its block/pattern/shortcode
is placed, and the DNA background only where its block is inserted or a
page explicitly enables it via the sidebar meta box.)

## 3 · Copy the binary/large assets from your repo (one-time)

Four text assets and one binary could not be packed into this project
automatically (binary transfers corrupt in this pipeline — the same class
of corruption that caused the PHP parse errors). Copy them from your repo
checkout, which already has everything:

```bash
# from your repo checkout, e.g. /workspaces/cas-ngs-biotech-blocks
REPO=/workspaces/cas-ngs-biotech-blocks
PLUGIN=<path-to-downloaded-plugin>/cas-ngs-header-footer

mkdir -p "$PLUGIN/assets/css" "$PLUGIN/assets/js" "$PLUGIN/assets/models"
cp "$REPO/assets/css/biotech-blocks.css"        "$PLUGIN/assets/css/"
cp "$REPO/assets/js/biotech-blocks-editor.js"   "$PLUGIN/assets/js/"
cp "$REPO/assets/js/biotech-blocks-engine.js"   "$PLUGIN/assets/js/"
cp "$REPO/assets/models/dna.glb"                "$PLUGIN/assets/models/"
cp "$REPO/blocks/act2-bento-grid/render.php"    "$PLUGIN/blocks/act2-bento-grid/"
```

Then re-zip the plugin folder and upload it. The `?cas-debug=1` notice
confirms when **all biotech assets present**. Without
`biotech-blocks-editor.js` the biotech blocks are not editable in the
editor; without the engine/CSS they render unstyled; without `dna.glb`
the 3D helix has no model (the blocks still function).

## 2 · Add the header (Appearance → Editor → Patterns)

1. **Appearance → Editor → Patterns → CAS-NGS** → insert
   **"CAS-NGS Top Dock Header"** into your **Header template part**
   (or insert the "CAS-NGS Top Dock Header" block from the inserter).
   The header is a **dynamic block**: PHP renders it from your settings on
   every request, so the pattern inserts cleanly into template parts in
   block themes (Twenty Twenty-Five included) — no "unexpected or invalid
   content", no recovery step, and the same instance stays in sync
   everywhere it appears.

2. Select the header block — everything is edited in the **block sidebar**:

   | Panel | Controls |
   | --- | --- |
   | **Brand** | Site icon from the **media library** (empty = leaf monogram), company wordmark text, wordmark on/off |
   | **CTA button** | show/hide, button text, button link |
   | **Tabs & dropdowns** | per tab: label, link, "current page" highlight; per dropdown link: label, link, small caption line; add/remove tabs and links freely |

3. Save the template part. The dock's appearance and animations are
   unchanged — only the content source moved from code to block attributes.

**Classic theme?** `<?php cas_ngs_header(); ?>` after `wp_body_open()` in
`header.php`, or `[cas_ngs_header]` in content.

## 3 · Add the footer

**Appearance → Editor → Patterns → CAS-NGS** → **"CAS-NGS Sylva Footer"**
into the **Footer template part**. Title, description, email, button, all
three link columns (one link per line; select a word + toolbar link
button), chip text and copyright are click-to-edit; strand/letter toggles
in the sidebar. Classic: `cas_ngs_footer()` / `[cas_ngs_footer]`.

## 4 · The biotech blocks & background animation

Registered by `includes/cas-ngs-biotech-blocks.php` from the untouched
folders in `blocks/` — their own editor UI (`biotech-blocks-editor.js`),
styles and render templates load exactly as before, so they remain fully
editable in the visual editor:

| Block | Inserter name | Shortcode |
| --- | --- | --- |
| `cas-ngs/header-top-dock` | Act 0: Animated Top Dock Header | `[cas_top_dock]` |
| `cas-ngs/interactive-pipeline-hero` | Act 0: Interactive 3D Pipeline Hero | `[cas_pipeline_hero]` |
| `cas-ngs/dna-background` | 3D DNA Helix Background | `[cas_dna_background]` |
| `cas-ngs/act1-hero-sequencer` | Act 1: Hero Sequencer Terminal | `[cas_ngs_act1]` |
| `cas-ngs/act2-bento-grid` | Act 2: Bento Services Grid | `[cas_ngs_act2]` |
| `cas-ngs/act3-process-timeline` | Act 3: Process Timeline | `[cas_ngs_act3]` |
| `cas-ngs/act4-cta-banner` | Act 4: Conversion CTA Banner | `[cas_ngs_act4]` |

`[cas_ngs_full_page]` renders the whole sequence (dock + DNA background +
Acts 1–4) at once. The dock reads the **"Top Dock Header Navigation"**
menu location (registered by the plugin) — child items become dropdown
cards automatically, with a rich fallback nav when no menu is assigned.

### Headers in Twenty Twenty-Five — no more "Attempt recovery"

- The **CAS-NGS Header** block is *dynamic* (server-rendered from its
  sidebar settings), so inserting the pattern into the Header template
  part never triggers block-validation errors.
- The biotech **Act 0 Top Dock** is a normal dynamic block too: add it to
  the Header template part (or any page). When it is present on a page,
  the suite adds the `cas-has-top-dock` body class and the bundled CSS
  hides TT5's default site header *on that page only* — opt-in, not
  site-wide.

### Note on `[cas_ngs_header]`

This suite reserves `[cas_ngs_header]` for the editable CAS-NGS Header
block. The ThreeUI dock uses `[cas_top_dock]` instead, so the two never
collide.

## 5 · Palette (hard-coded)

```
--sylva-t1 #865438 Dark Earth — text, headings, button fills, active borders
--sylva-t2 #af7853 Copper     — secondary borders, depth, canvas strokes
--sylva-t3 #d4996e Sand       — accents, active highlights
--sylva-t4 #e6ccb2 Beige      — card / pill surfaces, hover states
--sylva-t5 #ede0d4 Alabaster  — page background, light button text, glows
```

Re-theme via the `:root` block in `assets/header-footer.css` + the `C`
constant in `assets/header-footer.js` (keep in sync).

## 6 · Tuning & troubleshooting

- **Dock feel** — `assets/header-footer.js`: `SIGMA` (reach), `GROW`
  (max scale), `DROP` (downward droop in px — tabs grow from their top
  edge and drop down, never lifting into the window edge), `STIFFNESS`,
  `DAMPING`. **Bar geometry** — `.cas-dock-bar` in the CSS.
- **A packed block doesn't appear** — run the `?cas-debug=1` diagnostic;
  the folder must sit *directly* inside `blocks/` with `block.json` at
  the folder root and a valid `namespace/slug` name (details in
  `blocks/README.txt`).
- **Console diagnostics** — errors are prefixed `[CAS Header]` /
  `[CAS Footer]`; `?cas-debug=1` prints an engine status line.
- **Fonts** — Lexend + IBM Plex Mono load as `cas-ngs-hf-fonts`; dequeue
  that handle if your theme provides them.

## 7 · Fixing the three common symptoms

### Blocks don't appear in the inserter

1. In the editor, click **+** → search for **"CAS"**, or open the
   **"CAS-NGS Suite"** category (header + footer) and the
   **"CAS-NGS Biotech Blocks"** category (Acts 0–4, DNA background,
   pipeline hero).
2. The **biotech blocks only work if you copied the repo assets** from
   step 3 above — specifically `assets/js/biotech-blocks-editor.js`
   (registers their editor UI) and `assets/css/biotech-blocks.css`.
   Without those two files the biotech blocks can't render or edit.
3. If the header/footer still don't show, open the browser **DevTools →
   Console** while the editor loads and look for a red JavaScript error.
   A failed `block.js` stops its blocks from registering. Paste any error
   here and it can be fixed directly.

### Header looks huge / "several pages long" in the editor

That was the SVGs rendering without dimensions before the stylesheet
applied. All inline SVGs now carry explicit `width`/`height`, so icons stay
correctly sized in the editor even before styles load. Make sure you have
the latest `block.js` and `cas-ngs-header-footer.php` (both updated). If a
*biotech* block (e.g. the Act 0 dock) looks oversized, it's missing
`biotech-blocks.css` — copy it per step 3.

### Parse error returns when switching themes

A parse error is a corrupted PHP file on disk — the theme doesn't change
PHP files, so this means the copy on disk is still corrupted (or an old
copy is still active). Diagnose with evidence, not guessing:

```bash
cd "/home/ncge4060/Local Sites/casngs-fresh/app/public/wp-content/plugins/cas-ngs-header-footer"
php -l cas-ngs-header-footer.php
php -l includes/cas-ngs-biotech-blocks.php
for f in blocks/*/render.php; do php -l "$f"; done
```

- Every file prints `No syntax errors detected` → activate; you're done.
- Any file reports an error → that file is corrupted on disk. Replace it
  by re-downloading, then confirm with:
  ```bash
  sed -n '68,72p' <the-file> | cat -A
  ```
  (`cat -A` reveals hidden/truncated bytes so the exact corruption is
  visible.) All plugin PHP files are now kept to short lines specifically
  to survive transfers.
