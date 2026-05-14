# BOLD Biosciences — Landing Page Handoff

This is a minimal Hugo static site for **BOLD Biosciences**. Only the landing page exists; the full site will come later. Your job: get this repo on GitHub and serving on GitHub Pages.

---

## What's in the repo

```
.
├── hugo.toml                       # Hugo site config
├── content/
│   └── _index.md                   # Home page front matter (title, tagline)
├── layouts/
│   ├── _default/baseof.html        # HTML shell (<html>, <head>, <body>)
│   └── index.html                  # Home page template
├── static/
│   └── css/main.css                # Styles (black bg, white type, Helvetica)
├── .github/
│   └── workflows/hugo.yml          # GitHub Actions: build + deploy to Pages
├── .gitignore                      # Ignores public/, resources/, OS junk
├── index.html                      # Static preview (mirror of rendered output) — not used by Hugo
└── README.md                       # Short dev/build/deploy notes
```

### Design

- **Wordmark:** "BOLD Biosciences" — single size, weight contrast (900 / 300), tight tracking, baseline-aligned.
- **Tagline:** "Something amazing is brewing!"
- **Palette:** pure black background, white foreground, low-opacity white for secondary text.
- **Type:** Helvetica Neue → Helvetica → Arial system stack. No web fonts to load.
- **Footer:** small copyright line, top-ruled.

---

## Prerequisites for the deployer

- [Hugo extended](https://gohugo.io/installation/) `>= 0.128.0` (only needed for local previewing; the GitHub Action installs its own copy).
- `git`, a GitHub account, and `gh` CLI (optional but convenient).

---

## Deployment steps (do these in order)

### 1. Initialize git and verify the site builds locally

```bash
git init
git add .
git commit -m "Initial commit: BOLD Biosciences landing page"

# optional sanity check
hugo --minify
# should produce ./public with an index.html + css
```

If `hugo --minify` fails, stop and report the error — don't push a broken site.

### 2. Create the GitHub repo

Two options. Pick based on the URL the user wants:

**Option A — project page** at `https://<user-or-org>.github.io/<repo>/`
```bash
gh repo create boldbiosciences/landing --public --source=. --remote=origin --push
```

**Option B — user/org page** at `https://boldbiosciences.github.io/`
The repo MUST be named exactly `boldbiosciences.github.io`:
```bash
gh repo create boldbiosciences/boldbiosciences.github.io --public --source=. --remote=origin --push
```

If `gh` isn't available, create the repo in the GitHub UI, then:
```bash
git remote add origin git@github.com:<owner>/<repo>.git
git branch -M main
git push -u origin main
```

### 3. Enable GitHub Pages with the Actions source

In the repo: **Settings → Pages → Build and deployment → Source: GitHub Actions**.

(Do NOT pick "Deploy from a branch" — this site builds with Hugo via the workflow in `.github/workflows/hugo.yml`.)

With `gh` CLI:
```bash
gh api -X POST "repos/<owner>/<repo>/pages" \
  -f "build_type=workflow" \
  || gh api -X PUT "repos/<owner>/<repo>/pages" -f "build_type=workflow"
```

### 4. Confirm `baseURL` in `hugo.toml`

Open `hugo.toml` and set `baseURL` to the final URL:

- User/org page → `https://boldbiosciences.github.io/`
- Project page → `https://<user-or-org>.github.io/<repo>/`
- Custom domain → `https://<your-domain>/`

The workflow passes `--baseURL` from GitHub's Pages config at build time, so for Pages the value in `hugo.toml` is mostly informational — but keep it accurate.

Commit + push the change if you edit it:
```bash
git add hugo.toml
git commit -m "Set baseURL"
git push
```

### 5. Trigger / watch the deploy

The push to `main` will trigger the **Deploy Hugo site to Pages** workflow. Watch it:
```bash
gh run watch
```
or in the GitHub UI: **Actions** tab.

When the `deploy` job finishes, the live URL is in the job summary (also under **Settings → Pages**).

### 6. (Optional) Custom domain

If the user wants `boldbio.com` (or similar):

1. Drop a file at `static/CNAME` containing only the apex/host:
   ```
   boldbio.com
   ```
2. Commit + push.
3. In **Settings → Pages → Custom domain**, enter the same domain and save.
4. At the DNS registrar:
   - Apex (`boldbio.com`) → A records to GitHub Pages IPs:
     `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - Or `www.boldbio.com` → CNAME `<owner>.github.io.`
5. Wait for DNS, then enable **Enforce HTTPS** on the Pages settings page.

---

## Local development

```bash
hugo server -D
# open http://localhost:1313
```

Edits to `content/`, `layouts/`, and `static/` hot-reload.

---

## Editing the landing copy

The title and tagline live in `content/_index.md`:

```yaml
---
title: "BOLD Biosciences"
tagline: "Something amazing is brewing!"
---
```

The HTML wordmark itself ("BOLD" + "Biosciences") is hard-coded in `layouts/index.html` because the two words are styled independently. Change it there.

---

## Future-site notes

When it's time to grow this into the full site:

- Add pages under `content/` (`content/about.md`, `content/research/_index.md`, etc.).
- Either flesh out `layouts/` further, or install a Hugo theme and set `theme = "..."` in `hugo.toml`.
- The deploy workflow doesn't need to change — every push to `main` rebuilds.

---

## Sanity checklist before handing back to the user

- [ ] `hugo --minify` builds cleanly with no errors or warnings.
- [ ] Repo is pushed to GitHub on `main`.
- [ ] Pages source is set to **GitHub Actions**.
- [ ] First workflow run completed successfully (green check).
- [ ] Live URL renders: black background, "BOLD Biosciences" wordmark, "Something amazing is brewing!" tagline, no console errors.
- [ ] `baseURL` in `hugo.toml` matches the live URL.
- [ ] (If custom domain) DNS resolves and HTTPS is enforced.

Hand back: the live URL + the repo URL.

