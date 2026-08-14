# CLAUDE.md

Guidance for AI assistants (and humans) working in this repository.

## What this is

The official website of **Naše Rtyně** — a Czech independent-candidates
association ("sdružení nezávislých kandidátů") running in the 2026 municipal
elections in the town of Rtyně. The site is a **static, single-page marketing
site** served via **GitHub Pages** at the custom domain **nasertyne.cz**.

- No build step, no framework, no package manager. Files are served as-is.
- All user-facing content is in **Czech** (`lang="cs"`). Keep new copy in Czech.
- Deployment is automatic: pushing to the `main` branch publishes the site.

## Tech stack

- **HTML/CSS/vanilla JS** built on the [Start Bootstrap "Agency" v7.0.12](https://startbootstrap.com/theme/agency)
  theme (Bootstrap 5.2.3).
- **Jekyll** only implicitly — GitHub Pages runs it, but the site uses no Jekyll
  layouts, includes, or front matter. `_config.yml` exists purely to exclude
  files from the published output.
- **Self-hosted fonts** (Montserrat, Roboto Slab) under `assets/fonts/` — no
  Google Fonts fetch.
- External runtime dependencies loaded via CDN: Bootstrap JS bundle
  (jsDelivr), Font Awesome 6.3.0 icons, and GoatCounter analytics.

## Repository layout

```
index.html         Main single-page site (~1300 lines). All primary content + inline JS.
404.html           Custom 404 page (Agency-styled, noindex).
dotaznik/index.html Full-screen wrapper that iframes a Google Form survey (forms.gle/...).
old/               Archived previous landing page (single full-width image). Not linked from the live site.
style.css          The Agency theme's compiled CSS (~13k lines). Vendored/third-party — avoid editing directly.
custom.css         Project-specific overrides (brand colors, navbar, sections). Edit styles HERE.
script.js          Theme JS: navbar shrink-on-scroll + Bootstrap ScrollSpy + mobile nav collapse.
_config.yml        Jekyll exclude list (keeps .git, markdown, and old assets out of the build).
CNAME              Custom domain: nasertyne.cz
assets/
  favicon.svg
  fonts/           Self-hosted woff2 + fonts.css
  img/
    team/          Candidate portraits (.webp), one per person.
    about/         About-section photos.
    aktuality/     News-card images.
    logos/         Trust/partner logos.
    header-bg.{png,webp}, navbar-logo.png, close-icon.svg
```

## index.html structure

The homepage is one file with these `<section>` anchors (also the nav targets):

- `#page-top` / `header.masthead` — hero
- `#dotaznik` — survey call-to-action (links to `/dotaznik`)
- `#vize` — vision + quote block
- `#priority` — priority cards (flex row)
- `#program` — accordion program details, currently **hidden** (nav item has
  `d-none`; JS already wired for when it's re-enabled)
- `#about` — about the association
- `#team` — candidate grid, one commented block per candidate
- `#aktuality` — news cards
- `#election-countdown` — live countdown to **2026-10-09T14:00:00**; auto-hides
  when expired
- `#contact` — contact form

All page JavaScript lives in **two places**: `script.js` (theme behaviors) and
a single large inline `<script>` at the bottom of `index.html` (analytics
tracking, election countdown, and the contact-form AJAX handler).

## Key conventions

- **Edit styles in `custom.css`, not `style.css`.** `style.css` is the vendored
  Agency theme. Brand tokens live at the top of `custom.css`:
  `--brand-blue`, `--brand-blue-alt`, `--brand-yellow`, `--header-bg`.
- **Content is Czech.** Match the existing tone and keep `lang="cs"`.
- **Accessibility is maintained deliberately:** skip link, `aria-hidden` on
  decorative icons (auto-applied by JS), `aria-current` on the active nav link,
  ARIA live regions on form success/error messages, `visually-hidden` countdown
  fallback text. Preserve these when editing markup.
- **Images:** prefer **`.webp`** (team/about/aktuality already use it). Team
  portraits are named `first-last.webp` (lowercase, hyphenated, ASCII-folded,
  e.g. `dominik-sadlo.webp`). Place the file in `assets/img/team/` and add a
  matching commented candidate block in `#team`.
- **Performance:** the header background and key fonts are `<link rel="preload">`ed
  in `<head>`. Keep such hints in sync if you rename those assets.

## Forms & integrations

- **Contact form** (`#contactForm`) posts to **FormSubmit.co**
  (`https://formsubmit.co/ajax/nase.rtyne@gmail.com`) via `fetch`. Validation is
  Bootstrap's `needs-validation` / `was-validated`; success and error states
  toggle hidden `<div>`s. `_cc` hidden input allows extra recipients.
- **Survey** (`/dotaznik`) is a full-viewport iframe embedding a Google Form
  (`forms.gle/...`). To change the survey, update the iframe `src` in
  `dotaznik/index.html`.
- **Analytics:** [GoatCounter](https://nase-rtyne.goatcounter.com) via the
  `data-goatcounter` script tag. Custom events are fired through the
  `analyticsEvent(name, props)` helper in the inline script (CTA clicks, scroll
  depth, section views, nav clicks, form results, countdown, etc.). Reuse this
  helper for any new tracking rather than calling GoatCounter directly.

## Development workflow

There is nothing to install or build. To work on the site:

- Preview locally by opening `index.html` in a browser, or serve the folder with
  any static server (e.g. `python3 -m http.server`) so root-relative paths like
  `/dotaznik` resolve.
- Optionally run Jekyll to mirror GitHub Pages exactly, but it is not required.
- **Deploy = merge to `main`.** GitHub Pages rebuilds automatically; the `CNAME`
  file keeps the `nasertyne.cz` domain.

### Git / branching (for automated agents)

- Do development on the assigned feature branch; do **not** push directly to
  `main` without explicit permission.
- Push with `git push -u origin <branch-name>`.
- Do not open a pull request unless the user explicitly asks.

## Gotchas

- `style.css` is huge and machine-generated — searching it is fine, but hand-editing
  risks breaking the theme. Override in `custom.css` instead.
- The `#program` section and its nav link are intentionally hidden (`d-none`);
  don't "fix" this without checking intent. The countdown auto-hides after the
  election date — expected behavior.
- `old/` is a dead archive kept for reference and is not linked anywhere on the
  live site.
- Meta tags (Open Graph / Twitter / Schema.org JSON-LD) are duplicated across
  `index.html`, `404.html`, and `dotaznik/index.html`. Update titles, URLs, and
  images consistently when changing branding.
