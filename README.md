# Hugo Compose — PWA

A progressive web app for creating photo posts and blog posts on any Hugo site
directly from an iPhone (or any browser), via the GitHub API. No backend server
required.

## How it works

1. Pick a content type from the home screen (Photo Post, Blog Post, Training
   Note, Drafts or Posts).
2. Fill in the form — use `#hashtags` in photo captions for tags, Markdown in
   blog bodies.
3. Tap **Post** / **Publish** / **Save** — the app resizes images to ≤ 2000 px
   JPEG, then commits everything to GitHub in a **single commit** using the Git
   Trees API. GitHub Actions picks up the commit and deploys the site.
4. If you're offline (or tap **Save draft** at any time) the content is saved
   locally to IndexedDB. Open **Drafts** from the home screen to resume editing
   or submit later when you have a connection.

Each photo post creates a Hugo page bundle at:
```
<photos-path>/YYYY/MM/DD-slug/
  index.md        ← TOML or YAML front matter (title, date, description, tags)
  feature.jpg     ← cover / first photo
  image-2.jpg     ← additional photos (if any)
  …
```

Blog posts go to `<posts-path>/YYYY/MM/DD/`. Paths, front matter format
(TOML/YAML) and an optional notes data file are all configurable in Settings.

## Running locally

```sh
cd site/static/compose
python3 -m http.server 8080
# Open http://localhost:8080 in Safari, then "Add to Home Screen"
```

When deployed via Hugo, it lives at `/compose/`.

## First-time setup

1. Open Settings (gear icon) and enter:
   - **GitHub token** — a Personal Access Token with `contents:write` scope on the repo
   - **Owner** — your GitHub username
   - **Repository** — your Hugo site repo
   - **Branch** — `main` (or whichever branch triggers your deploy)
   - **Posts path** — e.g. `content/posts` (default)
   - **Photos path** — e.g. `content/photos` (default)
   - **Front matter format** — TOML or YAML
2. Tap **Save**.

The token is stored in `localStorage` in your browser — it never leaves your device.

## Drafts storage

Drafts are saved in IndexedDB (`dp_drafts_db`) and include any photos as File/Blob
objects — no base64 bloat, no localStorage quota issues. Drafts persist across
app reloads but are scoped to this origin.

## Generate proper app icons (iOS)

For a proper camera icon, open `make-icons.html` in a browser and click each
download button, then replace the files in this directory.

```sh
open make-icons.html   # macOS
```

Download and save:
- `icon-192.png`
- `icon-512.png`
- `icon-180.png`  ← used as `apple-touch-icon` on iOS

## GitHub token scopes

Create a **fine-grained** PAT at github.com → Settings → Developer settings →
Personal access tokens → Fine-grained:
- Repository access: your Hugo site repo only
- Permissions → Contents: **Read and write**
