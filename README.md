# BentleyHQ — Live Dashboard

Phone-only life productivity app. Self-updating from Claude chats via GitHub.

## Architecture

```
index.html  →  shell. Deploy once. Never edit on phone.
data.json   →  state. Push from this folder every session. Phone fetches it on load.
```

The shell fetches `./data.json` on load, caches it in localStorage for offline, and persists checkbox state in localStorage (key: `bhq.checks`). When Claude updates `data.json` and pushes, the next phone refresh pulls the new state.

## One-time setup (do this NOW, ~5 minutes)

### 1. Create the GitHub repo (web UI)

Go to https://github.com/new and create:
- Repository name: `bentleyhq` (or anything you want)
- Visibility: **Public** (required for free GitHub Pages)
- Do NOT initialize with README (we already have one)
- Click **Create repository**

Copy the URL it shows you. Looks like:
```
https://github.com/YOUR_USERNAME/bentleyhq.git
```

### 2. Push from this folder (terminal)

The repo is already initialized, files are committed, and the branch is `main`. You only need to add the remote and push. Open Terminal, paste these. Replace `YOUR_USERNAME` with yours.

```bash
cd "/Users/$(whoami)/Documents/90 Day Lead Generation Framework/bentley-hq"
git remote add origin https://github.com/YOUR_USERNAME/bentleyhq.git
git push -u origin main
```

(Adjust the `cd` path if your folder lives somewhere else.)

If git asks for credentials, use a Personal Access Token (not password). Generate at:
https://github.com/settings/tokens?type=beta

### 3. Turn on GitHub Pages

In the repo on github.com:
1. Click **Settings** (top right of the repo page)
2. Click **Pages** in the left sidebar
3. Under "Build and deployment":
   - Source: **Deploy from a branch**
   - Branch: **main** / `/ (root)`
4. Click **Save**

Wait ~60 seconds. Your URL will be:
```
https://YOUR_USERNAME.github.io/bentleyhq/
```

### 4. Add to home screen

On iPhone:
1. Open the URL in Safari
2. Tap the share button (square with up arrow)
3. Scroll down → **Add to Home Screen**
4. Name it "BentleyHQ" → Add

Now it lives on your home screen like an app.

## Daily flow (after setup)

Each session, Claude updates `data.json` here. Then run:

```bash
cd "/Users/$(whoami)/Documents/90 Day Lead Generation Framework/bentley-hq"
git add data.json
git commit -m "data update $(date +%Y-%m-%d)"
git push
```

That's it. Open the app on your phone, tap **↻ Sync**, fresh state loads.

## Persistence behavior

- **Checkboxes** persist via localStorage on your phone. They survive refreshes, app closes, even data.json updates. Stored under key `bhq.checks` keyed by checklist item ID.
- **Data cache** persists via localStorage so the app still loads when offline (with a "cached" indicator). Fresh data overrides on next successful fetch.
- **Sync button** force-fetches `data.json` with cache-busting. Use this after Claude pushes an update.

## Reset checkbox state (rare)

In Safari: Settings → Safari → Advanced → Website Data → search "github.io" → swipe delete that entry. Or in DevTools console: `localStorage.removeItem("bhq.checks")`.

## Files

- `index.html` — the shell (deploy once)
- `data.json` — the state (push every session)
- `README.md` — this file

## Why this architecture

- No backend needed
- Free hosting forever (GitHub Pages)
- Works offline (localStorage cache)
- Updates push via standard git
- Single HTML file = zero build step
- Mobile-first, dark theme, optimized for one-thumb use
