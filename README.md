# Quarto Dashboard Demo

A teaching example showing how to build an **interactive dashboard** from a Jupyter notebook using [Quarto](https://quarto.org/docs/dashboards/) and deploy it to **GitHub Pages**.

The dashboard uses the Gapminder dataset with Plotly Express to demonstrate rows, columns, value boxes, tabsets, and multiple pages.

## Files

- `dashboard.ipynb` — the dashboard notebook (exercise + working cells)
- `_quarto.yml` — Quarto project config (needed so `quarto publish` works without extra args)
- `requirements.txt` — Python dependencies for rendering
- `.github/workflows/publish.yml` — CI workflow that builds and publishes to GitHub Pages

## Local preview

### 1. Install Quarto + Python dependencies

Using [micromamba](https://mamba.readthedocs.io/) (or conda):

```bash
micromamba create -n dashboard -c conda-forge quarto python=3.11 -y
micromamba activate dashboard
pip install -r requirements.txt
```

Verify:

```bash
quarto --version       # 1.6 or newer
quarto check jupyter   # confirms Jupyter integration
```

### 2. Preview

```bash
quarto preview dashboard.ipynb
```

This opens a live-reloading browser tab. Edit the notebook and the dashboard updates on save.

### 3. Render a static build

```bash
quarto render dashboard.ipynb
```

The output HTML and assets are written to `_site/` (or next to the notebook, depending on your `_quarto.yml`).

## Deploy to GitHub Pages

This repo includes a workflow at `.github/workflows/publish.yml` that renders the dashboard and publishes it to the `gh-pages` branch on every push to `main`.

### One-time setup

1. Push this repo to GitHub.
2. In your repo: **Settings → Pages → Build and deployment → Source: Deploy from a branch**, then select the `gh-pages` branch and `/ (root)` folder.
3. Make sure **Settings → Actions → General → Workflow permissions** is set to **Read and write permissions** (so the workflow can push the rendered site).

### How the workflow works

1. Checks out the repo
2. Installs Quarto (`quarto-dev/quarto-actions/setup`)
3. Installs Python 3.11 and the packages in `requirements.txt`
4. Renders the dashboard and pushes the output to the `gh-pages` branch (`quarto-dev/quarto-actions/publish`)

### Trigger a deploy

Push to `main`, or run the workflow manually from the **Actions** tab (`workflow_dispatch`).

Your dashboard will be live at:

```
https://<your-username>.github.io/<repo-name>/
```

## Alternative deployment targets

Quarto can publish to several other hosts via `quarto publish`:

| Target | Command |
|--------|---------|
| GitHub Pages (manual) | `quarto publish gh-pages dashboard.ipynb` |
| Netlify | `quarto publish netlify dashboard.ipynb` |
| Quarto Pub | `quarto publish quarto-pub dashboard.ipynb` |
| Posit Connect | `quarto publish connect dashboard.ipynb` |

Because the dashboard renders to **static HTML**, you can also host the contents of `_site/` on any static file server (S3, Cloudflare Pages, GitLab Pages, ...).

## Troubleshooting

- **Plots don't appear** — make sure the YAML raw cell includes `execute: enabled: true`, and that Plotly uses `pio.renderers.default = "iframe"`.
- **Layout cells show up as text** — they must be **Raw** cells, not Markdown cells. This applies to the YAML header, all `## Row` / `# Page` headings, and `::: {.card} ... :::` blocks.
- **Workflow fails with "command not found: quarto"** — ensure `quarto-dev/quarto-actions/setup@v2` runs before the publish step.
- **`gh-pages` branch not created** — run `quarto publish gh-pages` once locally, or confirm the workflow has write permissions (see one-time setup above).

## References

- [Quarto Dashboards](https://quarto.org/docs/dashboards/)
- [Publishing with GitHub Actions](https://quarto.org/docs/publishing/github-pages.html#github-action)
- [quarto-actions on GitHub](https://github.com/quarto-dev/quarto-actions)
