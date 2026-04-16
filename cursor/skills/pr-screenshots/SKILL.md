---
name: pr-screenshots
description: Capture UI screenshots of changed pages and post them as a GitHub PR comment. Use when a PR touches any UI template, view component, or CSS/styling — mandatory, no exceptions. Requires playwright and an orphan branch for image hosting (see Setup).
---

# PR Screenshots

Capture screenshots of UI changes and post them as a PR comment. Screenshots are hosted on an orphan branch in the repo itself — no CDN uploads required, works for private repos.

## Setup (one-time per repo)

### 1. Install playwright

```bash
npm install -g playwright
playwright install chromium
```

### 2. Create the orphan branch

```bash
cd /tmp && mkdir pr-screenshots-init && cd pr-screenshots-init
git init && git checkout --orphan pr-screenshots

cat > README.md << 'EOF'
# PR Screenshots

This orphan branch hosts screenshots for PR review comments.
Images here are referenced in PR comments and can be cleaned up periodically.
EOF

echo "*.DS_Store" > .gitignore
git add . && git commit -m "Initialize pr-screenshots branch"
git remote add origin git@github.com:<OWNER>/<REPO>.git
git push origin pr-screenshots
```

## Before Capturing — Verify Assets Build

If working in a worktree or fresh clone, CSS/JS won't build without node_modules:

```bash
# Check dev server output for asset errors
# If you see "Could not resolve topbar/tailwindcss" or similar:
cd <project>/assets && npm install
```

Restart the dev server after installing. **Screenshots without styles are useless.**

## Step 1 — Ensure dev server is running

The dev server must be running on the PR branch:

```bash
# Check if running
lsof -i :4000 | grep LISTEN || echo "not running"

# Start it (Elixir/Phoenix example):
cd <WORKTREE_PATH>
cd assets && npm install && cd ..
source .env
mix ecto.migrate
mix phx.server
```

## Step 2 — Capture screenshots

### Simple pages (no auth)

```bash
npx playwright screenshot http://localhost:<PORT>/path /tmp/screenshot.png
npx playwright screenshot --full-page http://localhost:<PORT>/path /tmp/screenshot.png
```

### Authenticated pages

Use a script that logs in and screenshots in the same browser context. `npx playwright screenshot` doesn't persist cookies between calls.

```bash
cat > /tmp/screenshot-auth.mjs << 'EOF'
import { chromium } from 'playwright';

const PORT = process.env.PORT || 4000;
const BASE = `http://localhost:${PORT}`;

const browser = await chromium.launch();
const context = await browser.newContext({ viewport: { width: 1280, height: 900 } });
const page = await context.newPage();

// Adapt this to your project's dev auth bypass
await page.goto(`${BASE}/dev/login`);
await page.waitForLoadState('networkidle');

// Capture target pages
await page.goto(`${BASE}/your/page`);
await page.waitForLoadState('networkidle');
await page.waitForTimeout(500);
await page.screenshot({ path: '/tmp/page.png', fullPage: true });

await browser.close();
EOF
PORT=4000 node /tmp/screenshot-auth.mjs
```

Adapt the auth step to your project (dev auto-login route, cookie injection, etc.).

## Step 3 — Push to orphan branch

```bash
export PATH="$HOME/.local/bin:$PATH"
PR_NUMBER=<NUMBER>
OWNER=<OWNER>
REPO=<REPO>

cd /tmp && rm -rf pr-screenshots-work && mkdir pr-screenshots-work && cd pr-screenshots-work
git clone --single-branch --branch pr-screenshots git@github.com:${OWNER}/${REPO}.git .

mkdir -p pr${PR_NUMBER}
cp /tmp/<screenshot>.png pr${PR_NUMBER}/

git add pr${PR_NUMBER}/
git commit -m "Add PR #${PR_NUMBER} screenshots"
git push origin pr-screenshots
```

## Step 4 — Post as PR comment

```bash
gh pr comment ${PR_NUMBER} -R ${OWNER}/${REPO} --body "## Screenshots

**Description of view**
![label](https://github.com/${OWNER}/${REPO}/blob/pr-screenshots/pr${PR_NUMBER}/<screenshot>.png?raw=true)
"
```

## Naming conventions

- Directory: `pr<NUMBER>/` (e.g. `pr155/`)
- Files: `descriptive-name.png` (e.g. `product-detail-employee.png`, `settings-page.png`)
- Commit: `Add PR #<NUMBER> screenshots`

## Checklist

1. Dev server running on the PR branch with CSS/JS assets built
2. Screenshots captured for every changed view (both states if the change is interactive)
3. Screenshots named descriptively
4. Pushed to `pr<NUMBER>/` on the `pr-screenshots` orphan branch
5. PR comment posted with raw GitHub URLs and clear labels

## Tips

- Use `fullPage: true` for pages with scrollable content
- Add `await page.waitForTimeout(2000)` for loading states or animations
- Capture at a consistent viewport (scripts above default to 1280×900)
- For responsive changes, set `viewport: { width: 375, height: 812 }` for mobile
- Clean up old `pr<N>/` directories periodically to keep the orphan branch lean
