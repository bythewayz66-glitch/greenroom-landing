# Greenroom — landing page

A static, single-page overview of the **Greenroom** GPT rewards platform prototype
(React + tRPC + Postgres).

This directory is a **ready-to-publish GitHub Pages site**. It is intentionally
dependency-free: one `index.html` with inline CSS, no build step, no external
assets, no CDN calls. That means it renders identically whether it is opened from
a file, a static host, or GitHub Pages — and there is nothing to go stale.

## Contents

| File | Purpose |
| --- | --- |
| `index.html` | The whole site: overview, earn loop, coin economy, game RTP table, trust/anti-fraud layer, scope notes and links. Inline CSS, no build step. |
| `.nojekyll` | Tells GitHub Pages to skip Jekyll. **Required** — without it, GitHub Pages runs the content through Jekyll, which ignores files and directories beginning with an underscore and can silently drop files. This site has none today, but the file costs nothing and prevents a confusing future failure. |
| `README.md` | This file. |

## Relative paths

All internal navigation uses in-page anchors (`#loop`, `#economy`, `#games`,
`#trust`, `#links`), so the page works at any base path — including a project
site served from a subpath such as `https://<user>.github.io/<repo>/`.

The only absolute URLs are **outbound** links to the live prototype and the
project archive. Those are deliberate: they point at a different origin, so they
must remain absolute.

## Publish to GitHub Pages

Because this is a project site, the files must sit at the **root** of the
repository (or in `docs/`, with Pages configured to serve from there). The
simplest path is to put `index.html` at the repository root.

### Option A — GitHub web UI (no tooling required)

1. Create a new **public** repository, e.g. `greenroom-landing`.
2. Upload `index.html` and `.nojekyll` to the repository root
   (drag-and-drop onto the repo page works; `.nojekyll` must be added as a file
   named exactly `.nojekyll` with no extension).
3. Open **Settings → Pages**.
4. Under **Build and deployment → Source**, choose **Deploy from a branch**.
5. Set **Branch** to `main` and **Folder** to `/ (root)`, then **Save**.
6. Wait for the first build to finish (about a minute). The site publishes at:

   ```
   https://<your-username>.github.io/<repository-name>/
   ```

### Option B — Git CLI

```bash
git init
git add index.html .nojekyll README.md
git commit -m "Greenroom landing page"
git branch -M main
git remote add origin https://github.com/<your-username>/<repository-name>.git
git push -u origin main
```

Then enable Pages as in steps 3–5 above, or from the CLI with the GitHub CLI:

```bash
gh api -X POST "repos/<your-username>/<repository-name>/pages" \
  -f 'source[branch]=main' -f 'source[path]=/'
```

### Option C — REST API (what the agent uses)

```bash
# 1. create the repo
curl -X POST https://api.github.com/user/repos \
  -H "Authorization: Bearer $GITHUB_TOKEN" \
  -d '{"name":"greenroom-landing","private":false,"auto_init":false}'

# 2. commit the files (base64-encoded contents)
curl -X PUT https://api.github.com/repos/<user>/greenroom-landing/contents/index.html \
  -H "Authorization: Bearer $GITHUB_TOKEN" \
  -d '{"message":"Add landing page","content":"<base64>"}'

# 3. enable Pages from the repository root of main
curl -X POST https://api.github.com/repos/<user>/greenroom-landing/pages \
  -H "Authorization: Bearer $GITHUB_TOKEN" \
  -d '{"source":{"branch":"main","path":"/"}}'
```

## Verify the deployment

```bash
curl -sI https://<your-username>.github.io/<repository-name>/ | head -1
```

A `200` means the site is live. The API equivalent is
`GET /repos/{owner}/{repo}/pages`, which returns the `html_url` and the current
build `status`.

## Notes

- Pages requires the repository to be **public**, or a paid plan for a private
  one. A 404 immediately after publishing is usually just the first build still
  running — check **Actions** in the repository.
- The page is self-contained, so it also works verbatim on any static host
  (Netlify, Cloudflare Pages, S3) with no changes.
- This site describes a **prototype**: offer completion, payouts and email
  delivery are simulated there, and no real money moves through it. The page
  says so plainly rather than implying a live, money-moving service.
