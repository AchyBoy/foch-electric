# Foch Electric

Website for Foch Electric, licensed electrical contractor (licence 55468).

Single hand written `index.html`, no build step. Edit it and push, GitHub Pages
redeploys in about 90 seconds.

## Local preview

Use the `foch-site` entry in JobHub's `.claude/launch.json` (port 8645), or:

    python3 -m http.server 8645

## Two query flags

- `?review=1` outlines every fact still waiting on Andrew and lists them in a corner
  panel. Twelve open items at the time of writing.
- `?debug=1` shows the music log. Hand over the **Copy** output, not a screenshot.
  The first line stamps the build, so a stale CDN copy is obvious.

## Before it goes public

1. Clear the `?review=1` list.
2. Delete the `<meta name="robots" content="noindex, nofollow">` line in `index.html`.
3. Replace the `Disallow: /` in `robots.txt`.
4. Point `fochelc.com` at Pages. The MX records stay untouched, so email keeps working.

## Music

`music.m4a` has its level and both fades baked in. If it needs to be louder or
quieter, re-encode the file. Do not add a Web Audio gain node, that is what made it
play silently on iOS.
