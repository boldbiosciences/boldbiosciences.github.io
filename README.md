# BOLD Biosciences

Landing page for BOLD Biosciences. Built with [Hugo](https://gohugo.io/) and deployed to GitHub Pages.

## Local development

Requires Hugo extended `>= 0.128.0`.

```bash
hugo server -D
# open http://localhost:1313
```

## Build

```bash
hugo --minify
# output in ./public
```

## Deploy

Every push to `main` triggers `.github/workflows/hugo.yml`, which builds the site and deploys to GitHub Pages.

Pages source must be set to **GitHub Actions** under Settings → Pages.

## Editing copy

- Title and tagline: `content/_index.md`
- Wordmark markup (two-word weight contrast): `layouts/index.html`
- Styles: `static/css/main.css`
