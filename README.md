# opencode + DeepSeek: Beginner Guide

**opencode** is an AI coding assistant that runs in your terminal. You chat with it, and it can read your code, edit files, run commands, and review your changes.

**DeepSeek** is the AI model (the "brain") that opencode uses. You pay DeepSeek per use with an API key.

---

## 1. Install opencode

Pick **one** of these (macOS):

```bash
# Option A: official installer (recommended)
curl -fsSL https://opencode.ai/install | bash

# Option B: Homebrew
brew install sst/tap/opencode

# Option C: npm (needs Node.js)
npm install -g opencode-ai
```

Check it worked:

```bash
opencode --version
```

**"command not found"?** The installer puts opencode in `~/.opencode/bin`. Add that folder to your PATH:

```bash
echo 'export PATH="$HOME/.opencode/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Update later with:

```bash
opencode upgrade
```

---

## 2. Get a DeepSeek API key

An API key is like a password that lets opencode use DeepSeek on your account. **Never share it or put it in code/git.**

1. Go to **https://platform.deepseek.com** and sign in.
2. Add some credit under **Billing** (you pay per use; small amounts go a long way).
3. Open **API Keys** → **Create new API key** → copy it (you only see it once).

Now give the key to opencode:

```bash
opencode auth login
```

- Choose **DeepSeek** from the list
- Paste your key and press Enter

opencode saves it on your computer (`~/.local/share/opencode/auth.json`), so you only do this once.

Useful related commands:

```bash
opencode auth list              # see which providers are connected
opencode auth logout            # remove a saved key
```

> You can also do this from inside opencode by typing `/connect`.

---

## 3. Choose the model

First, see which DeepSeek models your key can use:

```bash
opencode models deepseek
```

You'll see something like:

```
deepseek/deepseek-flash      ← DeepSeek V4.1 Flash: fast & cheap, great for daily work
deepseek/deepseek-v4-pro     ← DeepSeek V4 Pro: smarter, slower, costs more
```

The format is always `provider/model`.

### Way 1: Set a default (recommended)

Edit the config file `~/.config/opencode/opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "deepseek/deepseek-flash"
}
```

Now every time you start opencode, it uses Flash.

### Way 2: Pick one just for this session

```bash
opencode -m deepseek/deepseek-v4-pro
```

### Way 3: Switch inside opencode

Type `/models` and pick one from the list.

**Tip:** Use **Flash** for most things. Switch to **Pro** for hard bugs or big design decisions.

---

## 4. Using opencode

### Start it

```bash
cd ~/Desktop/my-project      # go into your project folder first!
opencode                     # opens the chat screen
```

Then just type what you want in plain English:

```
explain what src/index.ts does
add a /health endpoint to the express server
why is this test failing? npm test
```

### The two modes: Plan vs Build

Press **Tab** to switch:

| Mode | What it does | When to use |
|---|---|---|
| **Plan** | Only reads & thinks. **Can't change files.** | Understanding code, planning a feature, safe exploring |
| **Build** | Can edit files and run commands | Actually making changes |

**Good habit:** Ask in **Plan** first → check the plan → switch to **Build** → "go ahead".

### Mention files with `@`

Type `@` and start typing a file name to attach it:

```
@src/api/routes.ts is there any route missing auth?
```

### Run shell commands with `!`

Start a message with `!` to run a terminal command directly:

```
!git status
!npm test
```

---

## 5. Important slash commands

Type `/` inside opencode to see all of them. These are the most useful:

### For reviewing and quality

| Command | What it does |
|---|---|
| `/review` | **AI code review.** Checks your changes for bugs, security issues, and mistakes. Great after an AI (or you) made lots of edits. |
| `/undo` | Undo the last change opencode made to your files. **Your safety net.** |
| `/redo` | Bring back what you just undid. |

### For setting up a project

| Command | What it does |
|---|---|
| `/init` | Scans your project and creates an `AGENTS.md` file describing it. **Run this once per project.** It helps the AI understand your code much better. |

### For managing chats

| Command | What it does |
|---|---|
| `/new` | Start a fresh chat (clean slate) |
| `/sessions` | See and reopen old chats |
| `/compact` | Summarize a long chat to save tokens (money) and keep the AI focused |
| `/share` | Create a link to share the chat with a teammate |
| `/export` | Save the chat to a file |

### Settings & other

| Command | What it does |
|---|---|
| `/models` | Switch AI model |
| `/connect` | Add an API key for a provider |
| `/themes` | Change colors |
| `/editor` | Write a long message in your code editor instead |
| `/help` | Show all commands and shortcuts |
| `/exit` | Quit (or press Ctrl+C) |

---

## 6. Handy terminal commands (outside the chat)

```bash
opencode                       # start in current folder
opencode -c                    # continue your last chat
opencode run "explain this repo"   # ask one question, get answer, exit (no chat screen)
opencode stats                 # see how many tokens/$ you've used
opencode models                # list all models you can use
opencode upgrade               # update opencode
```

---

## 7. Example workflow for a software engineer

```bash
cd ~/Desktop/eligibility-mike
opencode
```

Then inside opencode:

1. `/init` → first time only, so opencode learns your project
2. Press **Tab** → **Plan** mode → *"I need to add X. How should we do it?"*
3. Read the plan. Ask questions until it looks right.
4. Press **Tab** → **Build** mode → *"Go ahead"*
5. `!npm test` → make sure nothing broke
6. `/review` → let AI double-check the changes
7. Fix anything it finds → *"fix issue 2"*
8. Don't like a change? `/undo`
9. Commit with git when you're happy

---

## 8. Extras: MCP servers (optional, for later)

MCP servers give opencode extra abilities. Add them to `~/.config/opencode/opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "deepseek/deepseek-flash",
  "mcp": {
    "context7": {
      "type": "local",
      "command": ["npx", "-y", "@upstash/context7-mcp"],
      "enabled": true
    }
  }
}
```

| MCP | What it adds |
|---|---|
| **context7** | Up-to-date docs for libraries, so the AI doesn't use old APIs |
| **playwright** (`@playwright/mcp`) | Lets the AI open a browser and test your web app |

Manage them with:

```bash
opencode mcp list
```

---

## 9. Benchmarking (measure how well the AI performs)

A benchmark is a **fixed set of tasks** you give the AI, so you can compare results fairly, for example **Flash vs Pro**, or *"is `/review` actually catching bugs?"*

> **Costs money:** every benchmark run uses real tokens. Start small (3 tasks), then grow.

### What to measure

| Metric | Question | How to get it |
|---|---|---|
| **Success** | Did it solve the task? | Run your tests: `npm test` passes = ✅ |
| **Speed** | How long did it take? | Stopwatch, or `time` in front of the command |
| **Cost** | How many tokens / $? | `opencode stats` before and after |
| **Fix-ups** | How much did I fix by hand? | Lines changed in your follow-up `git diff --stat` |

### Benchmark A: Flash vs Pro

**Step 1: Pick 3–5 tasks** from real work. Good tasks are small, clear, and testable:

```
1. "Add input validation to the payers API route"
2. "Fix the failing test in test/api/dialer.test.ts"
3. "Add a /health endpoint that returns { ok: true }"
```

**Step 2: Make a safe practice branch** so nothing touches your real work:

```bash
cd ~/Desktop/eligibility-mike
git stash -u                   # save ALL unfinished work first, incl. new files (-u matters!)
git status                     # should say "working tree clean" before you continue
git checkout -b benchmark
```

> ⚠️ Don't skip the `-u`. Later steps use `git clean -fd`, which **deletes** new files that git isn't tracking yet. `git stash -u` keeps yours safe.

**Step 3: Run a task with Flash**, using `opencode run` (no chat screen, same every time):

```bash
opencode stats                                   # note the numbers
time opencode run -m deepseek/deepseek-flash "Add a /health endpoint that returns { ok: true }"
npm test                                         # did it work?
opencode stats                                   # note the new numbers
git diff --stat                                  # how much it changed
```

**Step 4: Reset and repeat with Pro:**

```bash
git checkout . && git clean -fd                  # ⚠️ throws away the AI's changes (only on the benchmark branch!)
time opencode run -m deepseek/deepseek-v4-pro "Add a /health endpoint that returns { ok: true }"
npm test
```

**Step 5: Fill in the scorecard** (copy this table into a notes file):

| Task | Model | Tests pass? | Time | Tokens / $ | Hand fixes | Notes |
|---|---|---|---|---|---|---|
| /health endpoint | Flash | ✅ | 45s | | 0 lines | |
| /health endpoint | Pro | ✅ | 1m 50s | | 0 lines | |
| payers validation | Flash | ❌ | | | | missed edge case |
| payers validation | Pro | | | | | |

**How to read it:** If Flash passes most tasks, use Flash daily and save Pro for the hard ones.

**When done**, go back to your real work:

```bash
git checkout -                 # back to your previous branch
git branch -D benchmark        # delete the practice branch
git stash pop                  # bring back your unfinished work
```

### Benchmark B: Is `/review` catching bugs?

You **plant bugs you already know about**, then see how many the AI finds.

**Step 1:** On a practice branch, add a few deliberate mistakes. Write each one down:

| # | Planted bug | Example |
|---|---|---|
| 1 | Missing auth check | New API route with no login check |
| 2 | Null crash | Using `user.name` when `user` can be empty |
| 3 | Sensitive data in logs | `console.log(patient)` with DOB / member ID |
| 4 | Frontend/backend mismatch | Backend returns `payerId`, UI reads `payer_id` |
| 5 | Wrong condition | `>` instead of `>=` |
| 6 | Weakened test | Test changed to always pass |

**Step 2:** Open opencode and run `/review`.

**Step 3:** Score it:

| # | Planted bug | Found? |
|---|---|---|
| 1 | Missing auth | ✅ |
| 2 | Null crash | ❌ |
| … | | |
| **Score** | | **4 / 6 found** |
| **False alarms** | Complaints about code that was fine | **1** |

**Goal:** High "found", low "false alarms". Try it with Flash and Pro to compare.

**Clean up:** `git checkout . && git clean -fd`, then delete the practice branch.

### Tips for fair benchmarks

- **Same tasks, same wording** for every model. Copy-paste the prompts.
- **Always start from a clean state** (reset git between runs).
- **Run each task 2–3 times** if you can. AI answers vary a bit each run.
- **Keep your scorecard.** Re-run it when a new model comes out to see if it's better.

### Public leaderboards (for general reference)

These compare models on standard tasks. They're useful for a big-picture view, but **your own benchmark tells you more about your project**:

| Leaderboard | What it tests |
|---|---|
| **SWE-bench** | Fixing real bugs from GitHub projects |
| **Aider Polyglot** | Coding tasks in many languages |
| **Terminal-Bench** | Doing tasks in a terminal, like opencode does |

---

## Tips to save money & get better results

- **Be specific:** "fix the null error in `src/api/dialer.ts` line 40" beats "fix bugs".
- **Small tasks:** one feature at a time works better than "build the whole app".
- **Use `/compact` or `/new`** when a chat gets long. Long chats cost more.
- **Check your usage** with `opencode stats`.
- **Company code:** make sure your team is OK with sending code to DeepSeek before using it on work repos.


## IMPORTANT REFRENCE LINKS

- [opencode platform]https://opencode.ai/()

- [Github Link](https://github.com/anomalyco/opencode/tree/v2)

- [Documentation](https://opencode.ai/v2/docs/skills/)
-
-
