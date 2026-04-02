# Field Notes: Jagdhütte — Settings & Permissions
**Project:** Jagdhütte client-portal (manuav-platform)
**Date:** 2026-04-02
**Sessions:** 7 (sessions 2-7 + 1 research session)
**Type:** field-notes

Based on 7 sessions of real-world usage. These are concrete gaps and fixes for the framework's settings configuration guidance.

---

## 1. Expand the settings.json template with real examples

The current template has placeholders but no concrete examples. Replace with a comprehensive template that the bootstrap fills in:

```json
{
  "enabledPlugins": {
    "// BOOTSTRAP": "Fill in based on project type and plugin-guide.md"
  },
  "permissions": {
    "allow": [
      "// ─── Universal (all projects) ───",
      "Read **/*",
      "Glob **/*",
      "Grep **/*",

      "// ─── Source code (BOOTSTRAP: set to project's source dirs) ───",
      "Edit {{SOURCE_DIR}}/**",
      "Write {{SOURCE_DIR}}/**",

      "// ─── Evaluation & reports ───",
      "Edit eval/**",
      "Write eval/**",
      "Write eval/**/*.mjs",
      "Write eval/**/*.js",

      "// ─── Documentation ───",
      "Edit CHANGELOG.md",
      "Write CHANGELOG.md",
      "Edit IMPROVEMENTS.md",
      "Write IMPROVEMENTS.md",
      "Edit AGENT-IMPROVEMENTS.md",
      "Write AGENT-IMPROVEMENTS.md",
      "Edit CLAUDE.md",
      "Write CLAUDE.md",
      "Edit docs/**",
      "Write docs/**",

      "// ─── Git (safe operations) ───",
      "Bash(git status*)",
      "Bash(git diff*)",
      "Bash(git log*)",
      "Bash(git add*)",
      "Bash(git commit*)",
      "Bash(git checkout*)",
      "Bash(git tag*)",
      "Bash(git stash*)",
      "Bash(git branch*)",

      "// ─── Build & test (BOOTSTRAP: fill in project-specific commands) ───",
      "Bash({{BUILD_COMMAND}}*)",
      "Bash({{LINT_COMMAND}}*)",
      "Bash({{TEST_COMMAND}}*)",
      "Bash({{DEV_COMMAND}}*)",
      "Bash({{TYPECHECK_COMMAND}}*)",

      "// ─── Common shell commands (prevent permission prompts) ───",
      "Bash(ls*)",
      "Bash(mkdir*)",
      "Bash(wc*)",
      "Bash(sed *)",
      "Bash(head *)",
      "Bash(tail *)",
      "Bash(grep *)",
      "Bash(awk *)",
      "Bash(sort *)",
      "Bash(diff *)",
      "Bash(echo *)",
      "Bash(cp *)",
      "Bash(mv *)",
      "Bash(curl -s*)",
      "Bash(python3 *)",
      "Bash(cat eval/*)",
      "Bash(cat << *)",

      "// ─── Temp files (for agent scripts — avoids heredoc security warnings) ───",
      "Bash(cat > /tmp/*)",
      "Bash(node /tmp/*)",
      "Bash(node eval/*)",
      "Write /tmp/**",
      "Edit /tmp/**",

      "// ─── Eval cleanup ───",
      "Bash(rm eval/*)",
      "Bash(rm /tmp/*)"
    ],
    "deny": [
      "// ─── Protected directories (BOOTSTRAP: fill in) ───",
      "Write {{PROTECTED_DIR_1}}/**",
      "Edit {{PROTECTED_DIR_1}}/**",
      "Write {{PROTECTED_DIR_2}}/**",
      "Edit {{PROTECTED_DIR_2}}/**",

      "// ─── Secrets ───",
      "Write .env*",
      "Write **/*.key",
      "Write **/*.pem",

      "// ─── Destructive operations ───",
      "Bash(rm -rf *)",
      "Bash(git push --force*)",
      "Bash(git reset --hard*)",
      "Bash(git checkout -- .)",

      "// ─── Database destructive ───",
      "Bash(drop *)",
      "Bash(DELETE *)",
      "Bash(TRUNCATE *)",

      "// ─── Package manager (user runs these manually) ───",
      "Bash(npm install *)",
      "Bash(npm uninstall*)",
      "Bash(yarn add*)",
      "Bash(yarn remove*)",
      "Bash(pnpm add*)",
      "Bash(pnpm remove*)"
    ]
  }
}
```

### Why block npm install?

Package installation changes the dependency tree and lockfile. This should be a conscious decision:
- Agents ask the user to run the command
- The user reviews what's being installed
- The user runs it manually from their terminal

If an agent edits `package.json` directly without running the install, the lockfile gets out of sync. Document this in CLAUDE.md as a hard boundary: "Never edit package.json dependency fields directly — if npm install is blocked, ask the user to run the command."

---

## 2. Document settings.json vs settings.local.json

Add to the bootstrap's output or a docs file:

### `.claude/settings.json` (committed to repo)
- Shared across the team
- Contains: plugin config, permission allow/deny, agent file boundaries
- Changes require team awareness
- Bootstrap generates this

### `.claude/settings.local.json` (gitignored)
- Personal to each developer
- Contains: one-off tool approvals, MCP server configs, additional directories
- Accumulates ad-hoc permissions during sessions (e.g., "Yes, and don't ask again for: npx playwright:*")
- Not managed by the framework — grows organically
- Can be cleaned up periodically

### What goes where:

| Setting | Where | Why |
|---------|-------|-----|
| File edit/write boundaries | settings.json | Team agreement |
| Git command permissions | settings.json | Everyone needs these |
| Build/test commands | settings.json | Project-specific, shared |
| npm install block | settings.json | Safety boundary |
| MCP server configs (Railway, Neon) | settings.local.json | Personal credentials |
| Additional directories | settings.local.json | Personal workspace |
| One-off tool approvals | settings.local.json | Auto-added by Claude Code |
| WebFetch domain approvals | settings.local.json | Auto-added per session |

---

## 3. Add heredoc / script creation guidance

Add to CLAUDE.md template under "Rules for All Agents":

```
- **Creating script files:** Use the Write tool to create the file, then Bash to run it.
  Don't use heredocs (`cat << 'EOF' > file`) — they trigger security warnings that require
  manual approval. Write the file first, then `Bash(node /tmp/my-script.js)`.
```

Add to tester and scrutinizer agent templates (any agent that creates Playwright scripts):

```
## Creating Script Files
When writing Playwright or Node.js scripts, use the **Write tool** to create the file,
then **Bash** to run it. Don't use heredocs (`cat << 'EOF' > file`), which trigger
security warnings and require manual approval.
```

Also add to bootstrap agent so it includes this when generating new agent definitions.

---

## 4. Add Playwright auth recipe

For projects using Clerk, Auth0, or similar OAuth providers:

### Problem
Playwright can't authenticate via Google OAuth — Google blocks automation browsers. Even using the real Chrome binary with `--enable-automation` flags gets rejected.

### Solution: Email/password login script

1. Ensure the auth provider supports email/password (alongside OAuth)
2. Create `scripts/save-playwright-auth.mjs`:

```javascript
import { chromium } from "playwright";
import { resolve, dirname } from "path";
import { fileURLToPath } from "url";
import { createInterface } from "readline";

const __dirname = dirname(fileURLToPath(import.meta.url));
const AUTH_FILE = resolve(__dirname, "..", "eval", "playwright-auth.json");
const EMAIL = process.env.AUTH_TEST_EMAIL || "default@example.com";
const PASSWORD = process.env.AUTH_TEST_PASSWORD || "";

async function askPassword() {
  if (PASSWORD) return PASSWORD;
  const rl = createInterface({ input: process.stdin, output: process.stdout });
  return new Promise((res) => {
    rl.question("Enter password: ", (answer) => { rl.close(); res(answer.trim()); });
  });
}

async function main() {
  const password = await askPassword();
  const browser = await chromium.launch({ headless: false });
  const context = await browser.newContext();
  const page = await context.newPage();

  await page.goto("http://localhost:3001/sign-in");
  await page.waitForTimeout(2000);

  // Adapt selectors to your auth provider's form
  await page.locator('input[name="identifier"]').fill(EMAIL);
  await page.locator('button:has-text("Continue")').click(); // or Fortsetzen, Weiter, etc.
  await page.waitForTimeout(1500);
  await page.locator('input[type="password"]').fill(password);
  await page.locator('button:has-text("Continue")').click();

  await page.waitForURL((url) => !url.pathname.includes("sign-in"), { timeout: 15000 });
  await context.storageState({ path: AUTH_FILE });
  console.log(`Auth state saved to: ${AUTH_FILE}`);
  await browser.close();
}

main().catch(console.error);
```

3. Gitignore the auth file: `eval/playwright-auth.json`
4. Run before sessions: `node scripts/save-playwright-auth.mjs`
5. Tester loads it: `browser.newContext({ storageState: AUTH_FILE })`

### Key learnings:
- Google OAuth blocks ALL Playwright-controlled browsers (even real Chrome)
- The auth provider must support email/password for automated testing
- Auth state expires — re-run the script when the tester reports sign-in redirects
- Short-lived JWT tokens auto-refresh via the provider's client-side SDK as long as the long-lived session cookies are valid

---

## 5. Add turn budget and timeout guidance

Add to orchestrator template:

### Turn Budget Management

Agents have hard turn limits (maxTurns in frontmatter). If they run out, they stop mid-task with no output.

**Principles:**
1. Scope briefs to fit the agent's budget
2. Tell agents their turn budget in the brief ("Budget: ~8-10 turns")
3. Split large tasks across multiple agent runs
4. If an agent fails to deliver, check if the scope was too broad before retrying

**Rough estimates:**
| Task | Turns | Timeout |
|------|-------|---------|
| Build/lint check | 2-3 | 3 min |
| Simple code change | 5-8 | 5-8 min |
| Complex refactor | 10-15 | 10-15 min |
| Visual testing (screenshots) | 8-12 | 5-10 min |
| CLS audit (multiple interactions) | 12-18 | 10-15 min |
| Code review | 8-15 | 5 min |
| Design proposal | 10-15 | 10-15 min |
| UX audit (2-3 pages) | 15-25 | 15 min |
| Full app audit | TOO LARGE | Split into focused runs |

---

## 6. Add testing capabilities documentation

For app-development archetype, document these tester capabilities:

### Static screenshots
Before/after of affected pages. Captures settled state.

### Interactive screenshots
Click tabs, open modals, trigger states. Orchestrator specifies which interactions.

### Two-pass capture
Pass 1: measure timing. Pass 2: redo action, capture screenshots at 20ms intervals proportional to duration. Catches every intermediate render state.

### CLS measurement
PerformanceObserver measures layout shift scores. Thresholds: <0.1 Good, 0.1-0.25 Needs improvement, >0.25 Poor.

### Interaction timing
Click-to-settled measurement. Thresholds: <200ms Instant, 200-500ms Fast, 500-1000ms Noticeable, >1000ms Slow.

### Test failure handling
Failed tests must be flagged prominently, diagnosed, retried if obvious fix, escalated if not.

---

## 7. Add cross-agent awareness section

Add to CLAUDE.md template:

```
## How Agents Share Work

All agents can read any file in the repo, including:
- **Images** — agents can view PNG, JPG screenshots
- **PDFs** — agents can read PDF documents
- **Reports** from other agents in eval/results/
- **Source code** — every agent should read actual code for context

Don't work in isolation. Read what other agents produced before starting your task.
```

---

## 8. Add design philosophy template

For app-development archetype, include in CLAUDE.md template:

```
## Design Philosophy

Think in states, not screens.
Transitions matter as much as destinations.
Investigate before patching.
Find issues proactively.
Small details compound.
Use CSS transitions for interactive states (hover, focus, color changes).
```

Each principle should be expanded with 2-3 sentences explaining WHY, as in the Jagdhütte CLAUDE.md.
