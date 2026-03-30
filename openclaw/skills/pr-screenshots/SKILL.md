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

// Set session cookie, use dev bypass, or navigate through login flow
await page.goto('http://localhost:<PORT>/protected-page');
await page.waitForLoadState('networkidle');
await page.screenshot({ path: '/tmp/page.png', fullPage: true });

await browser.close();
EOF
node /tmp/screenshot-auth.mjs
```

Adapt the auth approach to your project (cookie injection, dev auto-login env vars, etc.).

## Posting Screenshots to PRs (Private Repos)

For private repos, image URLs must use GitHub's blob storage:

1. Take screenshots to `/tmp/`
2. Open a throw-away issue, upload images via `gh issue create` body (GitHub hosts the images)
3. Extract the uploaded image URLs from the issue body
4. Close the throw-away issue
5. Post a PR comment with the hosted image URLs

```bash
# Take the screenshot
npx playwright screenshot --full-page http://localhost:<PORT>/page /tmp/screenshot.png

# Upload via temporary issue
ISSUE_URL=$(gh issue create -R <OWNER>/<REPO> --title "screenshot-upload-temp" \
  --body "![screenshot](/tmp/screenshot.png)" 2>&1)
# Extract image URL from created issue, then close it
```

For public repos, you can reference images directly or use any image hosting.

## Checklist

When taking PR screenshots:

1. **Start dev server** with the PR branch checked out
2. **Capture each changed view** — before and after if relevant
3. **Name screenshots descriptively** — `team-active-tab.png`, `settings-page.png`
4. **Post as PR comment** with clear labels for each screenshot
5. **Include both states** if the change involves interaction (e.g., tabs, modals)

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
