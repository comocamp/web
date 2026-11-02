# ComoCamp Website

The [ComoCamp website](https://comocamp.org/), built with [Hugo](https://gohugo.io/)
and deployed to GitHub Pages. CSS and JavaScript are bundled; no npm or asset build
step is required.

## Getting Started

Clone the repository, then choose **one** of the options below:

```bash
git clone https://github.com/comocamp/web.git
cd web
```

All options serve the site at **http://localhost:1313** with live reload.

### VS Code Dev Container (recommended)

Requires Docker with Docker Compose and the VS Code **Dev Containers** extension
(`ms-vscode-remote.remote-containers`).

1. Open the repository in VS Code and run **Dev Containers: Reopen in Container**.
2. If the server is not already running, start it in the container terminal:
   ```bash
   hugo server --bind 0.0.0.0
   ```

The [Dev Container](.devcontainer/devcontainer.json) reuses the Hugo Compose service,
opens `/site`, and installs Hugo editor extensions. After changing its configuration
or the Hugo version, run **Dev Containers: Rebuild Container**.

We keep Docker Compose even though there is only one service: it reads
`HUGO_VERSION` from [`.env`](.env), sharing the version pin with CI without
duplicating it in the Dev Container configuration. It also lets contributors
run the same environment without VS Code via `docker compose up`.

#### VS Code shortcuts

With the default VS Code keybindings:

| Action | Windows / Linux | macOS |
|---|---|---|
| Command Palette (e.g. **Dev Containers: Reopen in Container** or **Rebuild Container**) | `Ctrl+Shift+P` or `F1` | `Cmd+Shift+P` or `F1` |
| Toggle integrated terminal (run Hugo commands) | `` Ctrl+` `` | `` Ctrl+` `` |
| Preview the current Markdown file | `Ctrl+Shift+V` | `Cmd+Shift+V` |

The repository does not define custom keybindings or build/debug tasks.

### Docker Compose

With Docker and Docker Compose installed:

```bash
docker compose up
```

### Local Hugo

Install [Hugo Extended](https://gohugo.io/getting-started/installing/) matching
`HUGO_VERSION` in [`.env`](.env), then run:

```bash
hugo server
```

Docker and CI also use the version pinned in `.env`. Add `-D` to the Hugo server
command to preview drafts.

## Editing the Site

The site is a single page assembled by [`layouts/index.html`](layouts/index.html).

| File or directory | Purpose |
|---|---|
| `content/_index.md` | Global event metadata |
| `content/<section>/index.md` | Section content and structured data |
| `layouts/page/<section>.html` | Matching section template |
| `layouts/partials/` | Shared header, footer, and helpers |
| `hugo.toml` | Site settings, navigation, and section order (`Params.Sections`) |
| `static/` | CSS, JavaScript, images, and archived editions |
| `resources/_gen/`, `public/` | Generated output, ignored by Git |

Section content uses TOML front matter (`+++`) with `type = "page"`.
Store structured data (FAQs, tickets, team members) in front matter and prose
in the Markdown body. Use `draft = true` to hide a section from normal builds.

To add a section, create its content and template, add it to `Params.Sections`
in [`hugo.toml`](hugo.toml), and optionally add a `menu.main` navigation entry.

## Deployment

Push to `main` to deploy to **https://comocamp.org/** via
[GitHub Actions](.github/workflows/hugo.yaml), or start the workflow manually
from the Actions tab. Do not commit generated output.

For a local production build:

```bash
hugo --environment production --gc --minify --baseURL "https://comocamp.org/"

# Or with Docker:
docker compose run --rm hugo --environment production --gc --minify --baseURL "https://comocamp.org/"
```

Output is written to `public/`.

### Archived editions

Previous editions live in `static/<year>/` as self-contained snapshots. Hugo copies
them unchanged to `public/<year>/`, so existing archives can be fixed directly
without rebuilding an old source revision.

To create an archive:

1. Build that year's sources with `hugo --environment production --baseURL "/<year>/"`
   into a fresh output directory outside the repository (`--destination <directory>`).
2. Copy the output into `static/<year>/`, excluding older archives.
3. Check that `<base href="/<year>/">` and image paths such as
   `/<year>/hero/image.png` include the year prefix and all assets exist in the archive.

Never archive output from `hugo server`: it can contain localhost URLs and LiveReload.
Year-prefixed paths keep the archive working both locally and in production.

## Contributing

Create a branch (or fork), make your changes, and open a pull request describing them.

## License

This project is licensed under the [MIT License](LICENSE).

## Acknowledgments

- **[BootstrapMade](https://bootstrapmade.com/)**: For the design template.
- **[Hugo](https://gohugo.io/)**: For the static site generator.

For questions or feedback, contact us at [hello@comocamp.org](mailto:hello@comocamp.org).  
