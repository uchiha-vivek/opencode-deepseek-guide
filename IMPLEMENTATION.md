### How you can create your own custom commands using opencode and utilize Deepseek



### STEP: FIX PATH




```bash
echo 'export PATH="$HOME/.opencode/bin:$PATH"' >> ~/.zshrc
```


Make the folder

```bash
mkdir -p ~/.config/opencode/command
```

Open a new file


```bash
open -e ~/.config/opencode/command/security-review.md
```


If TextEdit says the file doesn't exist, run  `touch ~/.config/opencode/command/security-review.md`


***Paste the below md file***


```bash
---
description: Security and vulnerability review of uncommitted changes
agent: plan
---

Review ONLY the uncommitted changes in this repo for security vulnerabilities.

Steps:
1. Run `git status` and `git diff HEAD` to see what changed. Also read any new untracked files.
2. Open the full changed files when you need more context.
3. Do NOT edit any files. Report only.

Check for:
- Missing authentication/authorization on new or changed API routes
- Injection (SQL, command, path traversal), unsafe input without validation
- Secrets, API keys, or tokens hardcoded in code
- PHI leaks: patient names, DOB, SSN, member IDs in logs, errors, or API responses
- Insecure CORS, missing rate limits, weak JWT/session handling
- AI-agent mistakes: hallucinated imports, weakened/deleted tests, dead code, placeholder values

Output format:
- One line verdict: SAFE / NEEDS FIXES / CRITICAL
- Findings sorted by severity (CRITICAL, HIGH, MEDIUM, LOW), each with:
  file:line, what's wrong, how it could be exploited, how to fix
- If nothing is found, say so. Do not invent issues.

$ARGUMENTS
```


The above can be created for different scenarios



Explanation of the different parts of the file


- **description** - what shows up when you type in `/` in opencode
- **agent-plan** - runs in read only mode, so it can't edit files
- **$arguments** - anything you type after the command gets added here, for example "focus on the dialer routes"



### Add the aliases


```bash
echo "alias ai-review='opencode run --agent plan --command review'" >> ~/.zshrc
echo "alias ai-security='opencode run --agent plan --command security-review'" >> ~/.zshrc
```

### Reload your terminal

```bash
source ~/.zshrc
````


### check it's setup


```bash
opencode --version
alias | grep ai-
```


### How to implement it now


```bash
cd ~/Desktop/sample-project
```


```bash
ai-review
```


```bash
ai-security
```


How to see usage


```bash
ai-security "focus on the security vulnerabilities new payer playbooks API"
```











