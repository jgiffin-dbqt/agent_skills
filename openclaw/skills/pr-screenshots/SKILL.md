---
name: pr-screenshots
description: "Capture UI screenshots and attach them to GitHub PR comments. Use when a PR adds or changes UI and screenshots are needed, when asked to 'take screenshots', 'add screenshots to PR', or when the self-review checklist requires screenshots. Works with private repos."
metadata:
  {
    "openclaw": {
      "emoji": "📸",
      "requires": { "anyBins": ["playwright"] }
    }
  }
---

# PR Screenshots

Capture screenshots of UI changes and post them as GitHub PR comments.

## Prerequisites

- `playwright` CLI installed globally (`npm install -g playwright`)
- Chromium browser installed (`playwright install chromium`)
- Dev server running at the project's configured URL
- `gh` CLI authenticated for the target repo
- Orphan branch `pr-screenshots` exists in the repo (see Setup below)

## Setup — Orphan Branch (one-time per repo)

Screenshots are hosted on a `pr-screenshots` orphan branch in the repo itself.
This avoids GitHub CDN upload hacks and works for private repos.

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

## Taking Screenshots

Use playwright's built-in screenshot command:

```bash
# Basic screenshot
npx playwright screenshot http://localhost:<PORT>/path /tmp/screenshot.png

# Full page (scrollable area)
npx playwright screenshot --full-page http://localhost:<PORT>/path /tmp/screenshot.png

# Wait for a specific element before capturing
npx playwright screenshot --wait-for-selector ".my-component" http://localhost:<PORT>/path /tmp/screenshot.png

# Wait for timeout (ms) — useful for animations/loading states
npx playwright screenshot --wait-for-timeout 2000 http://localhost:<PORT>/path /tmp/screenshot.png
```

## Authenticated Pages

For pages behind auth, use a playwright script:

```bash
cat > /tmp/screenshot-auth.mjs << 'EOF'
import { chromium } from 'playwright';

const browser = await chromium.launch();
const context = await browser.newContext();
const page = await context.newPage();

// Dev login bypass (sets session cookie), then navigate to target page
await page.goto('http://localhost:<PORT>/dev/login');
await page.waitForLoadState('networkidle');

await page.goto('http://localhost:<PORT>/protected-page');
await page.waitForLoadState('networkidle');
await page.waitForTimeout(1000);
await page.screenshot({ path: '/tmp/page.png', fullPage: true });

await browser.close();
EOF
node /tmp/screenshot-auth.mjs
```

**Note:** `npx playwright screenshot` doesn't persist cookies between calls.
For authenticated pages, always use a script that logs in and screenshots in the same browser context.

Adapt the auth approach to your project (cookie injection, dev auto-login env vars, etc.).

## Posting Screenshots to PRs

### 1. Push to orphan branch

```bash
cd /tmp && rm -rf pr-screenshots-work && mkdir pr-screenshots-work
cd pr-screenshots-work
git clone --single-branch --branch pr-screenshots git@github.com:<OWNER>/<REPO>.git .

mkdir -p pr<NUMBER>
cp /tmp/screenshot-name.png pr<NUMBER>/screenshot-name.png

git add pr<NUMBER>/
git commit -m "Add PR #<NUMBER> screenshots"
git push origin pr-screenshots
```

### 2. Post PR comment with raw URLs

```bash
gh pr comment <NUMBER> -R <OWNER>/<REPO> --body "## Screenshots

![description](https://github.com/<OWNER>/<REPO>/blob/pr-screenshots/pr<NUMBER>/screenshot-name.png?raw=true)
"
```

### Naming convention

- Directory: `pr<NUMBER>/` (e.g., `pr155/`)
- Files: `descriptive-name.png` (e.g., `product-detail.png`, `mobile-view.png`)
- Commit message: `Add PR #<NUMBER> screenshots`

## Checklist

When taking PR screenshots:

1. **Start dev server** with the PR branch checked out
2. **Capture each changed view** — before and after if relevant
3. **Name screenshots descriptively** — `team-active-tab.png`, `settings-page.png`
4. **Push to orphan branch** — `pr<NUMBER>/` directory
5. **Post as PR comment** with raw GitHub URLs and clear labels
6. **Include both states** if the change involves interaction (e.g., tabs, modals)

## Tips

- Use `--full-page` for pages with scrollable content
- Use `--wait-for-timeout 1000` if the page has loading states
- Capture at a consistent viewport width (playwright defaults to 1280x720)
- For responsive changes, capture at multiple widths:
  ```bash
  cat > /tmp/mobile-screenshot.mjs << 'EOF'
  import { chromium } from 'playwright';
  const browser = await chromium.launch();
  const page = await browser.newPage({ viewport: { width: 375, height: 812 } });
  await page.goto('http://localhost:<PORT>/path');
  await page.screenshot({ path: '/tmp/mobile.png', fullPage: true });
  await browser.close();
  EOF
  ```
- Clean up old PR screenshot directories periodically to keep the branch lean
