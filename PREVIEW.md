# Local Preview

Renders the DesignPLUS Canvas pages in `pages/` and `templates/` locally, with the
same CSS/JS that style `dp-*`/`cp-*` classes on real Canvas.

## Run

From the repo root:

```sh
python3 -m http.server 8000
```

Open <http://localhost:8000/>.

Pick a file from the **Page** dropdown. Edit any `.html` in `pages/` or `templates/`,
then hit **↻ Reload** (or refresh) to see the change. The URL hash tracks the current
file, so you can bookmark/deep-link a specific page.

## What's loaded

- `assets/css/dp-content.css` — the DesignPLUS framework (defines every `dp-*`/`cp-*` class)
- `assets/css/bootstrap.css` — DesignPLUS depends on Bootstrap for grid/cards
- `assets/css/fontawesome.css` — icons (`fas fa-*`, `dp-icon`); webfonts load from the FA5 CDN
- `assets/css/canvas-*.css` — Canvas brand base (fonts, variables, common, wiki_page)
- `assets/css/IU.css` — IU institutional overrides
- `assets/js/jquery-3.7.1.min.js` — global jQuery (matches live Canvas; DP requires it)
- `assets/js/master_controls.js` — DesignPLUS legacy loader (sets init flags)
- `assets/js/dp-content.js` — DesignPLUS interactive behaviors (flashcard flips, accordions, tabs)
- `assets/js/336.js` — webpack chunk lazy-loaded by `dp-content.js`

### Interactivity requirements (why clicks work)

DesignPLUS widgets *build* their DOM on load, but their click handlers (flashcard flip,
accordion toggle) only wire up when these globals exist before `dp-content.js` runs:
- `window.jQuery` 3.7.1 as global `$`
- `window["DP_jQuery_…"] = window.jQuery` — DP binds widget handlers through this namespaced jQuery
- `window.DpConfig`, `window.DT_variables`, `window.dtVersion`
- `body` classes `dp-js-loaded dp-legacy-js-loaded bootstrap-loaded`, and NO `dp-editor`

The injected page sits inside a Canvas-shaped DOM
(`body.context-course_… > #content.ic-Layout-contentMain > #wiki_page_show > .show-content.user_content`)
so DesignPLUS rules scoped under `.user_content`/`#content`/`body` resolve correctly.

## Toggles in the preview bar

- **course-menu-expanded** — simulates the narrower content column when the Canvas course nav is open
- **dark mode** — adds `body.dp-dark-mode` to preview DesignPLUS dark styling

## Notes

- Shape/triangle images and Google Fonts in `dp-content.css` load from the Cidi Labs CDN,
  so a few requests hit the network; the core styling works offline.
- `assets/js/IU.js` (jQuery + a course-survey connector) is intentionally **not** loaded —
  it does nothing for rendering and would only add console noise.
- `ENV`/`INST` are stubbed with inert values so `dp-content.js` runs without a real Canvas session.
