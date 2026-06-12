# HANDOFF: Blocks — Game Boy-style puzzle PWA

## End goal
A live HTTPS URL Chief can text to anyone. They open it on a phone and play immediately.
Bonus: iOS users can Add to Home Screen for a full-screen, offline-capable app.
**Your job is deployment only. The build is complete. Do not modify game code unless a deploy blocker forces it.**

## What this is
A faithful homage to the original 1989 Game Boy falling-block puzzle, rendered at native
160x144 and upscaled with crisp pixel-perfect scaling (image-rendering: pixelated).
Single-page vanilla JS, zero dependencies, zero build step. Static files only.

## Files in this folder (all final)
| File | Purpose |
|---|---|
| index.html | Entire game: markup, CSS, JS in one file. iOS-optimized full-screen layout. |
| manifest.webmanifest | PWA manifest: standalone display, portrait lock, icons. |
| sw.js | Service worker, cache-first (`blocks-v1`). Enables offline play. |
| icon-180.png | apple-touch-icon (home screen icon on iOS). |
| icon-192.png / icon-512.png | Manifest icons (Android/desktop install). |

## What is DONE
- Full game: 7 pieces, GB scoring (40/100/300/1200 x level+1), GB speed curve from
  original frame tables, level up every 10 lines, next preview, line-clear flash,
  game-over curtain animation, pause, title screen with persistent top score (localStorage).
- Authentic 4-shade DMG green palette, 3 block sprite patterns, dithered walls,
  custom 3x5 pixel font.
- Touch controls: D-pad (left/right/down, up = rotate, center dot = hard drop),
  A/B rotate, Start = start/pause, Select = sound toggle (square-wave WebAudio SFX).
- Keyboard also works: arrows, X/Z rotate, space hard drop, Enter start/pause.
- iOS PWA plumbing: viewport-fit=cover + safe-area insets, black-translucent status bar,
  apple-mobile-web-app meta tags, scroll/zoom/long-press suppressed, SW registration,
  install hint banner shown in Safari (hidden once installed).
- Display sizing: canvas uses near-full device width — only 44px total horizontal chrome,
  capped at 480px. ~346pt wide on a standard iPhone (~89% of screen). Verified by code
  review; confirm visually on a real device after deploy.
- JS syntax-checked with node --check; manifest validated as JSON.

## What is NOT done (your tasks)
1. **Deploy to GitHub Pages** (preferred — Chief has gh authenticated):
   ```bash
   cd <this folder>
   git init && git add -A && git commit -m "Blocks PWA v1"
   gh repo create blocks-pwa --public --source=. --push
   gh api repos/{owner}/blocks-pwa/pages -X POST \
     -f "source[branch]=main" -f "source[path]=/" || \
   gh api repos/{owner}/blocks-pwa/pages -X PUT \
     -f "source[branch]=main" -f "source[path]=/"
   ```
   Live URL: https://<username>.github.io/blocks-pwa/
   Pages can take 1-2 min on first publish; poll with:
   `gh api repos/{owner}/blocks-pwa/pages --jq .status` until "built".
2. **Verify the deployment** (do not skip — test, don't eyeball):
   - `curl -I` the live URL: expect 200, content-type text/html.
   - curl manifest.webmanifest, sw.js, all three icons: expect 200 each.
   - Confirm HTTPS (required for the service worker).
3. **Report back to Chief** with the live URL and the iOS install steps:
   open in Safari -> Share -> Add to Home Screen.
4. **Optional (only if Chief asks)**: custom domain, e.g. blocks.blackplaguepdr.com —
   add CNAME file to repo, DNS CNAME record to <username>.github.io, enable in Pages settings.

## Constraints / gotchas
- Site must be served over HTTPS or the service worker will not register.
- All paths are relative — works at a subpath like /blocks-pwa/ with no changes.
- Any future edit to game files requires bumping `blocks-v1` in sw.js (cache name),
  or installed PWAs will keep serving the stale cache.
- iOS installs PWAs ONLY from Safari (not Chrome/Firefox on iOS).
- No analytics, no backend, no secrets anywhere in this project.

## Acceptance criteria
A stranger receives the URL by text, taps it, and is playing within 5 seconds.
