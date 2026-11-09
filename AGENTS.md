# Agent notes — social-media-assets

Public image host for Buffer. Clone path on the box: `/workspace/social-media-assets`.

## Auth for push (non-interactive)

- Remote URL is clean HTTPS (no token embedded).
- Local `core.askPass` points at `/workspace/social-media-assets-git-askpass.sh`.
- That helper reads `$SOCIAL_ASSETS_GITHUB_TOKEN` at runtime (fine-grained PAT, Contents R/W on this repo).
- Never print, echo, commit, or put the token in the remote URL.

Required env for any push:

```bash
export SOCIAL_ASSETS_GITHUB_TOKEN=…   # already injected on the box when configured
export GIT_ASKPASS=/workspace/social-media-assets-git-askpass.sh
export GIT_TERMINAL_PROMPT=0
```

## Per-image sequence

```bash
cd /workspace/social-media-assets
git pull --ff-only origin main
# copy generated PNG into rpm/ or securaone/ with a unique name (no Git LFS)
cp /workspace/path/to/image.png rpm/2026-11-09-slug.png
git add rpm/2026-11-09-slug.png
git commit -m "Add rpm/2026-11-09-slug.png"
git -c credential.helper= -c core.askPass="$GIT_ASKPASS" push origin main
URL="https://raw.githubusercontent.com/Davo-912/social-media-assets/main/rpm/2026-11-09-slug.png"
curl -sI "$URL" | tr -d '\r' | grep -iE 'HTTP/|content-type'
# expect 200 and content-type: image/png — then pass $URL to Buffer
```
