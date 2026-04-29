# Project Overview

**ComoCamp** (comocamp.org) is the annual Collaborative Modeling unconference in Vienna, organized by "Collaborate Vienna" (a non-profit). The website is a Hugo static site deployed to GitHub Pages.

---

# Tech Stack

- **Static site generator**: Hugo (version pinned in `.env` as `HUGO_VERSION`)
- **Deployment**: GitHub Actions → GitHub Pages (triggers on push to `main`)
- **Local dev**: Docker Compose (`docker-compose.yaml`) runs `hugomods/hugo:${HUGO_VERSION}` on port 1313
- **No external CSS frameworks or JS build steps** — no Sass, no npm bundler required

---

# Architecture: Single-Page Website

The site renders as a **single page** composed of sequential sections. The section order is defined in `hugo.toml`:

```toml
Sections = ["hero", "como", "camp", "community", "bring-your-topics", "location",
            "program", "participation", "faqs", "supporters", "supporters-2",
            "media-partners", "team", "we-are-looking-forward"]
```

Each section is a content page under `content/<section-name>/index.md`. The main layout (`layouts/index.html`) iterates over `Sections` and renders each via its dedicated template in `layouts/page/<section-name>.html`.

---

# Content Conventions

- Content lives in `content/<section>/index.md` using TOML front matter (`+++ ... +++`)
- Structured data (speakers, FAQs, tickets, team members, etc.) goes in front matter as TOML arrays — not in the markdown body
- The markdown body is used only for free-form prose within a section
- `type = "page"` must be set explicitly in front matter for Hugo to recognize the page
- `background` controls alternating section colors: `"white"`, `"alternate"`, or `"follow"` (inherits previous)
- `draft = true` hides a section from the rendered output

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

# Key Files

| Path | Purpose |
|---|---|
| `hugo.toml` | Site config, nav menu, section order |
| `content/_index.md` | Global front matter (event title, email, social links, organizers) |
| `content/<section>/index.md` | Per-section content and structured data |
| `layouts/index.html` | Single-page assembly |
| `layouts/page/*.html` | Per-section templates |
| `.env` | Hugo version pin |
| `docker-compose.yaml` | Local dev server |
| `.github/workflows/hugo.yaml` | CI/CD pipeline |
