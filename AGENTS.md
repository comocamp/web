# Project Overview

**ComoCamp** (comocamp.org) is the annual Collaborative Modeling unconference in Vienna, organized by "Collaborate Vienna" (a non-profit). The website is a Hugo static site deployed to GitHub Pages.

---

# Tech Stack

- **Static site generator**: Hugo (version pinned in `.env` as `HUGO_VERSION`)
- **Deployment**: GitHub Actions → GitHub Pages (triggers on push to `main`)
- **Local dev**: Docker Compose (`docker-compose.yaml`) runs `hugomods/hugo:${HUGO_VERSION}` on port 1313
- **No external CSS frameworks or JS build steps** — no Sass, no npm bundler required
- **Theme**: BootstrapMade "Impact". `static/assets/vendor/*`, `static/assets/js/main.js`, `static/assets/css/main.css` and the vendor/font links in `baseof.html` are theme code — **do not modify them**, even if parts look unused. Project-specific CSS goes into `static/assets/css/comocamp.css`.

---

# Architecture: Single-Page Website

The site renders as a **single page** composed of sequential sections. The section order is defined in `hugo.toml`:

```toml
Sections = ["hero", "como", "camp", "community", "bring-your-topics", "location",
            "program", "program-hands-on-intro-day", "program-open-space", "timekeeper",
            "participation", "faqs", "supporters", "supporters-2", "media-partners",
            "team", "we-are-looking-forward"]
```

Each section is a content page under `content/<section-name>/index.md`. The main layout (`layouts/index.html`) iterates over `Sections` and renders each via its dedicated template in `layouts/page/<section-name>.html`. Section pages are never rendered standalone: a `cascade` in `hugo.toml` sets `build.render = 'never'` for all pages, and taxonomies are disabled.

---

# Content Conventions

- Content lives in `content/<section>/index.md` using TOML front matter (`+++ ... +++`)
- Structured data (speakers, FAQs, tickets, team members, etc.) goes in front matter as TOML arrays — not in the markdown body
- The markdown body is used only for free-form prose within a section
- `type = "page"` must be set explicitly in front matter for Hugo to recognize the page
- `background` controls alternating section colors: `"white"`, `"alternate"`, or `"follow"` (inherits previous)
- `draft = true` hides a section from the rendered output
- `[Params.saveTheDate] enabled = true` in `hugo.toml` hides the ticket shop and FAQs until the shop is launched
- Alt texts for photos live in front matter (`[alt]` table in `camp`, `alt` list in `location`); sponsors/media partners have a `name` field used for the logo alt text
- SEO metadata lives in `layouts/_default/baseof.html` and is assembled from existing content: `_index.md` (`subtitle`, `description`, organizer), `program` (dates), `location` (`[venue]` block), `participation` (featured ticket) → JSON-LD `Event`

---

# Images

Process images inline in the section template (no shared partial):

```go-html-template
{{ with $.Resources.Get "photo.jpg" }}
{{ $x1 := .Process "resize 600x webp q80" }}
{{ $x2 := .Process (printf "resize %dx webp q80" (int (math.Min 1200 .Width))) }}
<img src="{{ $x1.RelPermalink }}" srcset="{{ $x1.RelPermalink }} 1x, {{ $x2.RelPermalink }} 2x" width="{{ $x1.Width }}" height="{{ $x1.Height }}" loading="lazy" decoding="async" alt="…">
{{ end }}
```

- WebP, `srcset` 1x/2x (2x capped at the original width), `loading="lazy"` + `decoding="async"` below the fold; the hero image uses `fetchpriority="high"` instead of lazy loading
- `width`/`height` only on photos. Omit them on CSS-constrained logos (`max-height`/`max-width`), where fixed attributes would distort the image
- Use `Fill` for uniform grids (e.g. location photos 3:2), `resize` otherwise
- Link detailed images (timetables, group photo) to the original so they can be viewed in full resolution

---

# Layouts

- `layouts/_default/baseof.html` — base HTML shell (head, header, footer wrapping)
- `layouts/index.html` — iterates over configured sections and embeds each
- `layouts/page/<section>.html` — one template per section; reads front matter params
- `layouts/partials/header.html`, `footer.html`, `background.html` — shared partials

When adding a new section:
1. Create `content/<section>/index.md` with `type = "page"`
2. Create `layouts/page/<section>.html`
3. Add the section name to the `Sections` array in `hugo.toml`
4. Optionally add a nav entry under `[menu]` in `hugo.toml`

---

# Tito Ticket Shop URLs

All Tito URLs (ticket shop, waiting list, small print / terms) live in `[Params.tito]` in `hugo.toml` (`shop`, `waitinglist`, `smallprint`) and are read via `site.Params.tito.*`. Never hardcode `ti.to` or `smallprint.tito.io` URLs in content or templates.

For a new edition, update the three URLs in `[Params.tito]`.

Tickets may still set an explicit `bookingLink` / `waitingLink` for non-Tito links (e.g. the hotel booking); otherwise they fall back to the Tito URLs.

---

# Running Locally

```bash
# With Docker (recommended, matches CI exactly)
docker compose up

# Or with a locally installed Hugo extended
hugo server
```

The dev server runs at http://localhost:1313 with live reload.

---

# Deploying

Pushing to `main` triggers the GitHub Actions workflow (`.github/workflows/hugo.yaml`), which builds with `hugo --gc --minify` and deploys to GitHub Pages automatically. No manual deploy step needed.

---

# Updating Hugo

The version is pinned once in `.env` (`HUGO_VERSION`) and read by Docker Compose, the Dev Container and CI.

1. Check the [release notes](https://github.com/gohugoio/hugo/releases) for deprecations/breaking changes
2. Bump `HUGO_VERSION` in `.env`
3. Build locally (`hugo --gc --minify` or `docker compose run --rm hugo --gc --minify`) and fix any `WARN`/`deprecated` output — the build must be warning-free
4. Rebuild the Dev Container if you use it, then push

---

# Key Files

| Path | Purpose |
|---|---|
| `hugo.toml` | Site config, nav menu, section order, Tito ticket shop (`Params.tito`) |
| `content/_index.md` | Global front matter (event title, email, social links, organizers) |
| `content/<section>/index.md` | Per-section content and structured data |
| `layouts/index.html` | Single-page assembly |
| `layouts/page/*.html` | Per-section templates |
| `.env` | Hugo version pin |
| `docker-compose.yaml` | Local dev server |
| `.github/workflows/hugo.yaml` | CI/CD pipeline |
