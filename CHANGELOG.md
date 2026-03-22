# Changelog

All notable changes to **beads_for_cass** are documented here.

This repository hosts a [beads_viewer](https://github.com/Dicklesworthstone/beads_viewer) (`bv`) static dashboard deployed to GitHub Pages for the "Cass Beads" project. It provides an interactive, fully offline-capable issue tracker and dependency-graph visualization for 3 issues.

- **Repository**: <https://github.com/Dicklesworthstone/beads_for_cass>
- **Live dashboard**: <https://Dicklesworthstone.github.io/beads_for_cass/>
- **GitHub repo created**: 2026-01-07
- **No releases or tags have been published.** All history is commit-based.

---

## 2026-02-21 — Repository Metadata and Licensing

Two housekeeping commits added licensing and social sharing support.

### License

- Added `LICENSE` with MIT + OpenAI/Anthropic Rider. This license grants standard MIT freedoms but explicitly restricts use by OpenAI, Anthropic, and their affiliates without express prior written permission from Jeffrey Emanuel.
- Commit: [`eb17e7e`](https://github.com/Dicklesworthstone/beads_for_cass/commit/eb17e7e80d7360b7073bdbb07c7038ca33de1daa) — 2026-02-21 23:35 EST

### Social Preview Image

- Added `gh_og_share_image.png` (1280x640) for consistent Open Graph social media previews when sharing the repository URL. Generated via the `gh-og-share-images` skill.
- Commit: [`588bb00`](https://github.com/Dicklesworthstone/beads_for_cass/commit/588bb0028eccb5d0b731aa9751eebbe9602723b0) — 2026-02-21 22:20 EST

---

## 2026-01-26 — Merge Remote Deployment

Reconciled diverged local and remote histories. The remote deployment (2026-01-25) contained newer data; the local state (2026-01-10) was preserved in a backup branch `backup-local-20260126-134410`.

- Merge commit: [`819b808`](https://github.com/Dicklesworthstone/beads_for_cass/commit/819b8084a906cbd9cf56c9403f9223e05dade922) — 2026-01-26 13:44 CST

### Changes merged

- Incorporated the 2026-01-25 dashboard refresh into the main branch (see below for full details of what changed).
- All application code unchanged; only data files and the auto-generated README were updated.

---

## 2026-01-25 — Dashboard Refresh: Phase 2 Scoring

Second `bv --pages` deployment, regenerating all data with materially improved scoring.

- Commit: [`0c77699`](https://github.com/Dicklesworthstone/beads_for_cass/commit/0c776998a58d6a9d5e23f019594c6a2c2aff718f) — 2026-01-25 06:06 EST

### Graph-Centrality Scoring (Phase 2)

- Enabled the `phase2_ready` flag (previously `false`), activating graph-centrality-based scoring throughout the triage pipeline.
- PageRank scores now populated. All three issues score at 100% centrality (expected, since the graph has zero inter-issue dependencies).
- Impact scores roughly doubled due to the PageRank contribution:
  - `issue-1`: 0.15 to 0.30
  - `issue-2`: 0.13 to 0.28
  - `issue-3`: 0.09 to 0.22

### Scoring Reason Enrichment

- New human-readable reasons added to triage output:
  - "High centrality in dependency graph (PageRank: 100%)" on all three issues.
  - "Low effort, high impact - good starting point" on `issue-1`.
- `issue-1` action text upgraded from "Start work on this issue" to "Quick win - start here for fast progress".

### Scoring Recalibration

- Urgency for `issue-1` reset from 0.5 to 0 (aging-based urgency removed).
- `time_to_impact` explanation updated from "Leaf node" to "On dependency chain (depth 1)".
- `time_to_impact_norm` for `issue-1` adjusted from 0.2625 to 0.3325.

### Data Updates

- SQLite database grew from 39,936 to 44,032 bytes with updated snapshots.
- Velocity window shifted forward two weeks (oldest week now 2025-12-01, up from 2025-11-17).
- Graph layout node positions reshuffled (Y coordinates rotated among the three issues).
- README auto-regenerated with updated scores, new reasons, and new generation timestamp.

---

## 2026-01-10 — Initial Deployment

First `bv --pages` deployment. Created the entire static dashboard from scratch.

- Commit: [`515c643`](https://github.com/Dicklesworthstone/beads_for_cass/commit/515c6435741fdc2766fe717f728d3f93195e17ee) — 2026-01-10 17:50 EST
- 32 files, 19,795 lines added.

### Interactive Dashboard (Single-Page Application)

- `index.html` (4,133 lines) — Main SPA shell built with Alpine.js and Tailwind CSS. Includes mobile web-app metadata, Content Security Policy for fully self-contained offline use, dark/light theme with instant first-paint (no flash), and Mermaid diagram support.
- `viewer.js` (3,472 lines) — Core viewer logic providing the issue list, detail panels, search, filtering, and time-travel functionality. Uses sql.js to run SQLite queries entirely in the browser via WASM, with OPFS caching for offline support and FTS5 full-text search.
- `styles.css` (2,374 lines) — Full stylesheet with dark and light theme variants.

### Dependency Graph Visualization

- `graph.js` (3,847 lines) — Production-quality force-directed dependency graph using D3 v7 and force-graph. Features WASM-powered graph metrics (PageRank, betweenness centrality), multiple view modes, rich node/edge interactions, Dracula-themed color palette, and accessibility support.
- `graph-demo.html` (863 lines) — Standalone demo page for the graph visualization, independently usable outside the main dashboard.

### Analytics and Scoring

- `charts.js` (761 lines) — Chart.js-powered analytics dashboard with burndown/burnup progress charts, label dependency heatmaps, priority distribution pie charts, and type breakdown bar charts.
- `hybrid_scorer.js` (88 lines) — Client-side graph-aware search ranking with configurable weight presets (`default`, `bug-hunting`, `sprint-planning`, `impact-first`, `text-only`). Mirrors server-side normalization logic.
- `hybrid_scorer.test.js` (45 lines) — Test suite for the hybrid scorer.

### WASM and Offline Infrastructure

- `wasm_loader.js` (154 lines) — WebAssembly loader for the Rust-compiled graph engine, with fallback handling and diagnostics tracking.
- `coi-serviceworker.js` (136 lines) — Cross-origin isolation service worker that injects COOP/COEP headers on GitHub Pages (which cannot set response headers), enabling SharedArrayBuffer for sql.js WASM performance. Uses `credentialless` COEP policy.

### Vendor Dependencies (all bundled for offline use)

- **WASM engines**:
  - `vendor/bv_graph.js` + `vendor/bv_graph_bg.wasm` (217 KB) — Rust-compiled graph layout and metrics engine.
  - `vendor/sql-wasm.js` + `vendor/sql-wasm.wasm` (655 KB) — sql.js SQLite engine for in-browser database queries.
- **Visualization**:
  - `vendor/d3.v7.min.js` — D3 v7 for graph rendering.
  - `vendor/force-graph.min.js` — Force-directed graph library.
  - `vendor/chart.umd.min.js` — Chart.js for analytics charts.
  - `vendor/mermaid.min.js` — Mermaid diagram rendering.
- **UI framework**:
  - `vendor/alpine.min.js` + `vendor/alpine-collapse.min.js` — Alpine.js reactive UI framework.
  - `vendor/tailwindcss.js` — Tailwind CSS runtime compiler.
- **Content rendering**:
  - `vendor/marked.min.js` — Markdown-to-HTML rendering.
  - `vendor/dompurify.min.js` — HTML sanitization for security.
- **Typography**:
  - `vendor/inter-variable.woff2` — Inter variable font.
  - `vendor/jetbrains-mono-regular.woff2` — JetBrains Mono monospace font.

### Project Data

- `beads.sqlite3` (39,936 bytes) — SQLite database containing 3 issues with full history, queryable client-side via sql.js.
- `beads.sqlite3.config.json` — Database metadata (chunking configuration, total size).
- `data/triage.json` — Graph-scored issue triage with priority rankings, per-issue score breakdowns (PageRank, betweenness, staleness, urgency, risk), and actionable recommendations.
- `data/project_health.json` — Aggregate health metrics: issue counts by status/type/priority, graph density, velocity tracking with 8-week weekly breakdown.
- `data/graph_layout.json` — Pre-computed node positions and per-issue graph metrics (PageRank, betweenness centrality, in/out degree).
- `data/meta.json` — Generation metadata (version 1.0.0, title "Cass Beads", 3 issues, 0 dependencies).
- `README.md` — Auto-generated executive summary with top priorities, quick wins, graph analysis, and a link to the live dashboard.
