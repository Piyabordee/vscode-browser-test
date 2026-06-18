---
name: vscode-browser-test
description: 'Use when the user wants to test, verify, screenshot, click, fill, or interact with a web app, an HTML mockup (e.g. `docs/mockup/**/*.html`), a local dev server at `127.0.0.1` / `localhost`, or any URL the agent can reach. Triggers on phrases like "test this page", "open in browser", "click the button", "verify the form", "screenshot the dashboard", "check responsive", "playwright test" (redirect to native tools). Use VS Code native browser agent tools (openBrowserPage, readPage, screenshotPage, clickElement, typeInPage, hoverElement, dragElement, handleDialog, navigatePage, runPlaywrightCode) instead of installing Playwright/Puppeteer npm packages.'
---

# VS Code Native Browser Test

## Why this skill exists

**Do NOT install Playwright, Puppeteer, headless Chrome, or any browser-automation npm package in this project.** VS Code already ships an integrated browser plus a full set of browser-agent tools that work on Windows, macOS, and Linux with zero install. This skill teaches the agent to use them.

If a user asks for "Playwright tests" or "headless browser tests", interpret the *intent* (verify a web page) and use the native tools below — do not run `npm install -D playwright`.

---

## When to load this skill

- User says: "test this page", "open it in the browser", "screenshot the dashboard", "click the X button", "fill the form", "verify the layout", "check responsive"
- User points at: `file://.../*.html`, `http://127.0.0.1:3000`, `http://localhost:3000`, any dev server URL
- User wants an **accessibility audit**, **form validation test**, **responsive layout check**, or **auth flow test** against any web page
- Target surface: any HTML mockup, any local dev server (Next.js, Vite, Astro, plain Express, etc.), or any reachable URL

---

## Prerequisites (one-time, per workspace)

Browser agent tools are **opt-in**. Confirm these before assuming the tools work:

1. **VS Code setting** `workbench.browser.enableChatTools` must be `true`.
   - If false, tell the user to set it in `settings.json` and reload.
   - This setting is org-managed in some environments — surface that if it's locked.
2. **Chat tools picker** must show all browser tools enabled, grouped under **Built-in > Browser**.
   - Chat view → Tools button (chat input area) → Built-in > Browser → all 10 toggled on.
3. Note: Browser agent tools are **experimental** and may change. Don't hard-code behavior on edge cases.

If prerequisites are not met, **do not** try to `npm install playwright` as a fallback. Ask the user to enable the setting or use the tools picker.

### Auto-enabling browser tools with `@browser`

If the tools are present in the runtime but disabled in the chat session, the user can **explicitly enable them for a single prompt** by adding the `@browser` reference at the start of their message:

```
@browser open http://127.0.0.1:3000 and screenshot the dashboard
```

This injects the browser tools into the current turn without requiring the user to open the tools picker every time. Useful for:

- One-off tests when the user only occasionally runs browser checks.
- Sharing the skill in chat sessions where tools are normally off.
- Avoiding the friction of toggling 10 switches in the tools picker.

> **Note:** `@browser` is a **per-turn** reference. It does not change the user's persistent tools-picker settings. Each new prompt needs `@browser` again unless the user keeps the tools enabled.

---

## The 10 native browser tools

> The agent does not need to "declare" these tools exist — they are already wired into the chat runtime. The skill's job is to teach **when and in what order to use them**.

| # | Tool | Purpose | When to reach for it |
|---|------|---------|----------------------|
| 1 | `openBrowserPage` | Open a URL or local file in the integrated browser | **First step** for every test session. Pass absolute `file://` URL for HTML mockups, `http://127.0.0.1:<port>` for dev servers. |
| 2 | `readPage` | Get a structured snapshot (DOM, ARIA, headings, links) | Before any interaction — understand the page before clicking anything. |
| 3 | `screenshotPage` | Capture viewport **or a specific element (via `selector`/`ref`)** as PNG | Before AND after each interaction, to prove visual state changed. Pass `ref`/`selector` to screenshot just one component. |
| 4 | `clickElement` | Click a button/link/element by ref or selector | User actions: submit, navigate, toggle, open menu. |
| 5 | `typeInPage` | Type into a focused input, or focus+type via ref/selector | Form fields, search boxes, text areas. Supports `key` parameter for Enter/Tab/etc. |
| 6 | `hoverElement` | Hover over an element | Reveal tooltips, dropdown menus, hover-only states. |
| 7 | `dragElement` | Drag from one element to another | Sortable lists, file drops, sliders, drag-and-drop reordering. |
| 8 | `handleDialog` | Accept/dismiss alert/confirm/prompt | Any modal dialog. For file choosers, pass `selectFiles` with absolute paths. |
| 9 | `navigatePage` | Go to URL, go back/forward, or reload | SPA route changes (`navigatePage type=url`), in-app navigation, refresh after code change. |
| 10 | `runPlaywrightCode` | Run arbitrary Playwright code in the page context | **Last resort** for: complex multi-step assertions, file upload via `setInputFiles`, evaluating JS, network interception, waiting for custom conditions. Do not use for simple clicks/typing. |

### Tool selection priority (cheapest → most powerful)

```
openBrowserPage → readPage → screenshotPage → clickElement/typeInPage/hoverElement/dragElement → handleDialog → navigatePage → runPlaywrightCode
```

Reach for `runPlaywrightCode` **only** when no higher-level tool can do the job. The native tools give the user a better visual confirmation and are easier to step through.

---

## Standard workflow (the build → test → debug loop)

Use this loop whenever the user asks to verify a web page works:

### Step 1 — Open the page
- HTML mockup: `openBrowserPage url=file:///absolute/path/to/your/mockup.html` (must be an absolute `file://` URL)
- Dev server: `openBrowserPage url=http://127.0.0.1:<port>` (verify the port from `package.json`, terminal output, or framework config)
- External: pass the full `https://...` URL

### Step 2 — Read before acting
- Call `readPage` to enumerate buttons, inputs, links, headings.
- Identify the element `ref` or stable selector you'll need for `clickElement` / `typeInPage`.

### Step 3 — Screenshot the baseline
- `screenshotPage` to capture the initial visual state.
- Save the mental model: "this is what the page looks like before my action."

### Step 4 — Interact
- Use the appropriate tool from the table above.
- **One screenshot per action**, in this order:
  1. Screenshot before
  2. Perform the action
  3. `readPage` or `screenshotPage` after to confirm the change

### Step 5 — Verify and report
- State what **passed** and what **failed** with concrete evidence (screenshot ref, error message, observed text).
- If a bug is found:
  1. Identify the smallest code change that fixes it.
  2. Apply the fix in the repo.
  3. `navigatePage type=reload` (or reopen the page) and re-run the failing step.
  4. Re-screenshot to prove the fix.

### Step 6 — Close out
- Summarize: actions taken, screenshots captured, bugs found, fixes applied, what still needs human review.

---

## Session isolation rules (important!)

- **Pages opened by the agent** run in a **private, in-memory, ephemeral session**. They do **not** share cookies, `localStorage`, or login state with the user's regular browser tabs.
- This is by design — protects the user's browsing data.
- If the test needs the user's logged-in state (e.g., a session cookie), the user must:
  1. Open the page themselves in the integrated browser (Command Palette → **Browser: Open Integrated Browser**).
  2. Navigate to the page and log in.
  3. Click **Share with Agent** in the browser toolbar.
  4. Tell the agent which shared page to use.
- **Shared pages** use the user's real session (cookies, login). **Agent-opened pages** do not. Don't conflate the two.

---

## Common scenarios

Use these recipes for the most common browser-verification asks. Adapt the URLs and selectors to the target under test.

### 1. Screenshot an HTML mockup for review
```
User: "screenshot the dashboard mockup"
→ openBrowserPage url=file:///absolute/path/to/docs/mockup/dashboard.html
→ screenshotPage
→ report the file path or describe the visual
```

### 2. Verify a form (login, signup, contact, etc.)
```
User: "test the login form"
→ openBrowserPage url=http://127.0.0.1:3000/login   (after the dev server is running)
→ readPage     → identify inputs
→ typeInPage   → fill username
→ typeInPage   → fill password
→ screenshotPage → before submit
→ clickElement  → submit button
→ handleDialog  → if a confirm/alert pops up
→ screenshotPage → after submit
→ verify redirect (or expected success state) on the next page
```

### 3. Test a stateful form with validation
- When the form has conditional rules (e.g. "comment required when rating < N"), test both branches:
  - **Happy path** — fill all required fields, submit, expect success.
  - **Failure path** — omit the conditional field, submit, expect an inline error and no navigation.
- Use `readPage` to confirm the error message text matches what the form is supposed to show.

### 4. Responsive layout check
- Screenshot at the default viewport.
- Use `runPlaywrightCode` to call `page.setViewportSize({width: 375, height: 812})` (mobile) and re-screenshot.
- Compare both screenshots; flag any layout breakage (overflow, hidden text, misaligned elements).
- Reset the viewport with `page.setViewportSize({width: 1280, height: 720})` when finished.

### 5. Accessibility quick audit
- `readPage` returns ARIA info — check:
  - All images have `alt` text.
  - Heading order is h1 → h2 → h3 (no skipped levels).
  - Interactive elements are reachable by keyboard.
  - Color contrast (visual check via screenshot).
- Use the `audit` skill for a fuller accessibility framework.

### 6. Test a local dev server (any framework)
```
Terminal A:  npm run dev   # or the framework's start command (background)
Chat:        openBrowserPage url=http://127.0.0.1:<port>
             readPage → verify page loaded
             navigatePage type=reload → re-test after a code change
```

If the port is unknown, read the terminal output to find the actual bound port before calling `openBrowserPage`.

---

## When NOT to use this skill

These tools are designed for **interactive, in-editor verification**. Do not use them for:

- **Large E2E test suites:** Not a replacement for Playwright/Cypress in CI. Use for ad-hoc and development-loop testing only.
- **Cross-browser testing:** The integrated browser is Chromium-based. For Firefox/Safari/Edge compatibility, use dedicated tools.
- **Headless CI/CD pipelines:** These tools require the VS Code UI and an agent session. They will **not** work in a headless CI environment (e.g., GitHub Actions without a display).
- **Visual regression testing at scale:** `screenshotPage` is great for manual checks, but lacks automated diffing of specialized visual-regression tools.
- **Performance testing:** No network timing, no performance metrics, no load generation.
- **Parallel test execution:** One agent session, one browser. No multi-page parallel runs.

---

## Troubleshooting

If the agent gets stuck or tools fail, check these common issues:

1. **Tool not found / not executing:**
   - Verify `workbench.browser.enableChatTools` is `true` in VS Code settings.
   - Open the Chat tools picker and ensure the specific tool (e.g., `clickElement`) is toggled on under **Built-in > Browser**.
2. **`ref` not found or stale:**
   - The page may have reloaded or changed dynamically. Call `readPage` again to get a fresh snapshot and new `ref` values.
3. **Action executed but nothing happened:**
   - The element might be covered by an overlay or not interactive. Try `hoverElement` first, or check if a dialog needs handling via `handleDialog`.
4. **Page content is empty or still loading:**
   - Use `runPlaywrightCode` with `await page.waitForLoadState('networkidle')` or `await page.waitForTimeout(2000)` before reading or screenshotting.
5. **"Share with Agent" not working:**
   - The user must open the page in the **VS Code integrated browser** (Command Palette → **Browser: Open Integrated Browser**), not their external browser, before clicking **Share with Agent**.
6. **Dev server returned blank page:**
   - Confirm `npm run dev` is actually running in the background. Read the terminal output to see the real port and any compile errors.
7. **CORS / network error in console:**
   - Expected for `file://` pages that fetch external resources. Not a tool failure. Check the page's own network handling.

---

## Anti-patterns — do NOT do these

| Don't | Why |
|-------|-----|
| `npm install playwright` / `npm install -D @playwright/test` | The native tools do everything Playwright can for an in-editor test loop, with zero install. Save the project the dependency. |
| `npx playwright install` | Downloads ~300MB of browser binaries. Native tools already have a browser. |
| Write a `*.spec.ts` Playwright file for ad-hoc verification | Use the native tools for one-off checks. Reserve Playwright for full E2E suites that need to run in CI. |
| Spawn a child process running Puppeteer | Same reason as Playwright. |
| Use `curl` to "test" a web page | `curl` returns HTML source, not a rendered page. Use the browser tools to see what the user sees. |
| Skip `readPage` and guess selectors | Always read the page first. Refs/selectors change between versions. |
| Skip the before/after screenshot | Without a baseline, you can't prove the action changed anything. |
| Try to share cookies between agent-opened and user-opened pages | Impossible by design. Use the **Share with Agent** flow for shared session state. |

---

## Quick reference: when the user says X, do Y

| User says | Action |
|-----------|--------|
| `"@browser ..."` at start of prompt | Browser tools are force-enabled for this turn. Proceed with `openBrowserPage` / `screenshotPage` / etc. directly. |
| "test this page" / "verify it works" | Open + read + screenshot + interact + verify |
| "screenshot X" | `openBrowserPage` → `screenshotPage` |
| "click the Save button" | `readPage` → find Save ref → `clickElement` → `screenshotPage` to confirm |
| "fill in the form" | `readPage` → list inputs → `typeInPage` each → `screenshotPage` |
| "does it work on mobile?" | Native tools + `runPlaywrightCode` to resize viewport, or instruct user to use DevTools device emulation |
| "check accessibility" | `readPage` + visual screenshot + (optional) `audit` skill for deeper checks |
| "playwright test" | **Do not install Playwright.** Use the 10 native tools above. If they hit a wall, escalate to the user. |
| "test the form submission" | Fill → submit → `handleDialog` if needed → verify redirect/state change |
| "open the mockup" | `openBrowserPage` with `file://` absolute path |

---

## Minimal end-to-end example

User: "open the dashboard mockup and verify the sidebar shows the expected menu items."

1. `openBrowserPage url=file:///absolute/path/to/docs/mockup/dashboard.html`
2. `screenshotPage` — baseline.
3. `readPage` — look for the sidebar nav, list its items.
4. If all expected items present (use `readPage` to enumerate the actual labels) → pass, report what was found.
5. If fewer or extra items → fail, report the exact text observed.
6. Summary in 2-3 lines: ✅ passed / ❌ failed, with the observed data.

---

## Related

- [VS Code browser agent testing guide](https://code.visualstudio.com/docs/copilot/guides/browser-agent-testing-guide) — upstream docs
- [Integrated browser in VS Code](https://code.visualstudio.com/docs/debugtest/integrated-browser)
- [Claude skills documentation](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) — how this skill is loaded by Claude Code
- [Agent Skills specification](https://agentskills.io) — community spec for portable skill format
