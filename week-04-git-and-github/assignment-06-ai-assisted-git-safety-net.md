# Assignment 6 — Building an AI-Assisted Git Safety Net (PR Ready Check)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In Week 2 you built Claude Code hooks that block a dangerous action *before* it happens (`PreToolUse`), and a restricted skill that could look but not touch (`allowed-tools` without `Write`). In this assignment you will discover that Git has the exact same idea, decades older: a **pre-commit hook** that blocks a commit before it's created.

You will build both halves of a real "PR Ready" workflow:

1. A **Git hook that follows fixed rules** — scans staged changes for hardcoded secrets and oversized files and refuses the commit. No AI involved, no guessing, just a rule that gives the same answer every time.
2. A **restricted Claude Code skill** (`/pr-ready`) that reads your staged diff and drafts a Pull Request title, description, and a short list of things worth a second look — the kind of judgment a fixed rule can't make (mixed changes, missing context, unclear intent). The skill never commits, pushes, or opens the PR. You do that yourself, using its draft as a starting point.

This mirrors the Agentic Loop from Week 3's Linux triage assignment: **Gather → Analyze → Human Act → Verify**. The hook and the skill both gather and analyze; only you act.

---

# Task 1 — Create a Branch with Realistic Risk

## Goal

On your own fork of this repository (the one you've been submitting your DMI work in since onboarding), create a new branch and stage a change that a real reviewer should catch: a hardcoded-looking secret and a leftover debug statement.

### What to do

```bash
git checkout -b feature/ai-pr-ready
```

Create a file `scripts/notify.sh` (or edit any existing script) that includes a fake AWS-style key and a debug `echo`, for example:

```bash
#!/bin/bash
# demo only — fake credential for this assignment, never a real key
AWS_ACCESS_KEY_ID=AKIA_REDACTED_KEY
echo "DEBUG: token is $AWS_ACCESS_KEY_ID"
```

Stage it with `git add`.

### Evidence

#### Screenshot 1 — `git status` showing the staged file on your new branch

![alt text](screenshots\sc1-T1-ass6-week4.png)

---

### Notes

**1. Why does this assignment use an obviously fake key instead of a real one?**

This assignment uses an obviously fake AWS key (like AKIA_REDACTED_KEY) for several critical reasons:

1. 🛡️ Security Risk Prevention
Real keys lead to real compromise: If a real AWS secret or access key is committed and pushed to a public GitHub repository, automated bots scan GitHub 24/7 to scrape credentials within seconds.

Financial & Infrastructure Damage: Scraped AWS keys can be used by attackers to launch expensive cloud instances (e.g., for crypto mining) or steal sensitive cloud data.

2. 🤖 Testing Secret Scanning & CI/CD Tooling
Detecting patterns without exposure: DevSecOps assignments often set up pre-commit hooks or automated secret-scanning tools (like GitGuardian, TruffleHog, or GitHub Secret Scanning).

Safe Triggering: Tools detect credentials using regular expressions (for example, AWS Access Key IDs always start with AKIA followed by 16 alphanumeric characters). The fake key matches this pattern to trigger the detector without risking real-world secrets.

3. 🚨 Best Practice Demonstration
Hardcoding vs. Environment Variables: The exercise simulates a common real-world mistake—hardcoding credentials inside code or debug scripts—so you can practice catching, removing, or ignoring them before making a Pull Request.

---

# Task 2 — Write a Real Git Pre-Commit Hook

## Goal

Create a tracked, shareable pre-commit hook that blocks a commit containing secret-like patterns or files over 1MB.

### What to do

Create `hooks/pre-commit` (tracked in the repo, not `.git/hooks/`, so teammates get it too):

```bash
#!/bin/bash
# hooks/pre-commit — blocks commits with likely secrets or oversized files
set -e

staged=$(git diff --cached --name-only --diff-filter=ACM)
blocked=0

for file in $staged; do
  if git diff --cached -- "$file" | grep -qE 'AKIA[0-9A-Z]{16}|-----BEGIN (RSA|OPENSSH|PRIVATE) KEY-----'; then
    echo "BLOCKED: possible secret in $file"
    blocked=1
  fi
  size=$(git cat-file -s "$(git rev-parse ":$file")" 2>/dev/null || echo 0)
  if [ "$size" -gt 1000000 ]; then
    echo "BLOCKED: $file is $(($size / 1000000))MB — over the 1MB limit"
    blocked=1
  fi
done

if [ "$blocked" -eq 1 ]; then
  echo "Commit rejected. Fix the issues above and try again."
  exit 1
fi
```

Point Git at the tracked hooks folder and make it executable:

```bash
chmod +x hooks/pre-commit
git config core.hooksPath hooks
```

### Evidence

#### Screenshot 2 — `hooks/pre-commit` open in VS Code showing the full script

![alt text](screenshots\sc2-T2-ass6-week4.png)

---

#### Screenshot 3 — Output of `git config core.hooksPath` confirming it points to `hooks`

![alt text](screenshots\sc3-T2-ass6-week4.png)

---

### Notes

**1. Why is `hooks/pre-commit` tracked in the repo instead of living only in `.git/hooks/`?**
The reason hooks/pre-commit (or a similar path like .githooks/pre-commit) is tracked in the repository root while .git/hooks/ is ignored by Git boils down to version control, team sharing, and Git security architecture.

1. .git/ Is Local to Each Machine (Not Tracked)
The .git directory contains your local Git database, configuration, and internal state.

Git never tracks or pushes anything inside .git/.

If you place a pre-commit script inside .git/hooks/, it stays only on your machine. When a teammate clones or pulls the repository, they won't get your hook script.

2. Team Standardization & Version Control
By creating a folder in the working directory (such as hooks/pre-commit or .githooks/pre-commit) and committing it:

Shared Enforcement: Everyone on the team gets the exact same linting, formatting, or secret-scanning rules.

Change History: If someone updates a quality check, they can commit the change in hooks/pre-commit and submit a Pull Request so the whole team reviews and inherits the update.
💡 How Git Connects Tracked Hooks to .git/hooks/
Because Git won't run tracked files directly out-of-the-box, projects connect tracked hook folders using one of these common patterns

---

**2. Compare this to `PreToolUse` from Week 2 Assignment 6. What does each one intercept, and what do they have in common?**

To compare the pre-commit Git hook to the PreToolUse hook/event from your assignment, it helps to look at what stage of execution each one acts on and the core purpose they share.

🤝 What They Have in Common
Pre-Execution Validation (Shift-Left Security): Both operate on a "check first, execute second" design pattern. They validate inputs before a permanent state change or side-effect occurs (creating a commit vs. executing a tool call).

Automated Guardrails: Both act as automated gates to enforce safety, security, and quality standards without relying on human memory or manual checks.

Short-Circuiting / Halting Execution: Both hooks return a failure status (or raise an exception) that immediately aborts the pending action if validation fails.

Policy Enforcement: They both enforce policy locally at the boundary—protecting repositories from dirty/dangerous code and protecting runtime systems from risky actions.

---

# Task 3 — Prove the Hook Blocks the Risky Commit

## Goal

Attempt to commit the staged file from Task 1 and show the hook rejecting it.

### Evidence

#### Screenshot 4 — Terminal showing `git commit` rejected with the hook's "BLOCKED" message naming the exact file

![alt text](screenshots\sc4-T3-ass6-week4.png)

---

### Notes

**1. Which line in `hooks/pre-commit` matched your fake key, and why did it match?**



Why it matched:

AKIA Prefix: The regular expression looks specifically for the prefix AKIA, which is the standard starting identifier for AWS Access Key IDs.

Character Set & Exact Length ([0-9A-Z]{16}): The pattern specifies exactly 16 uppercase alphanumeric characters immediately following AKIA.

The Fake Key: Your test credential AKIA_REDACTED_KEY consists of AKIA followed by 16 uppercase letters (ABCDEFGHIJKLMNOP), which is an exact 20-character regex match for this pattern.

Staged Changes Inspection (git diff --cached): The script checks the staged diff of scripts/notify.sh. Since the line AWS_ACCESS_KEY_ID=AKIA_REDACTED_KEY was added to the staging area, grep detected the pattern, set blocked=1, and aborted the commit with exit 1.

---

**2. Could this hook have caught a poorly-named variable that stores a secret without the `AKIA` prefix? What does that tell you about the limits of a fixed rule like this?**

No, it could not.

Because this hook relies strictly on exact regular expression patterns  and SSH key headers), any secret that does not strictly match those strings will bypass the check.

---

# Task 4 — Build the `/pr-ready` Skill

## Goal

Create a manually invoked Claude Code skill that reads your staged changes and produces a PR-readiness report and a draft PR description — without writing, committing, or pushing anything itself.

### What to do

Create `.claude/skills/pr-ready/SKILL.md` with frontmatter restricting it to read-only inspection tools:

```markdown
---
name: pr-ready
description: Reviews staged Git changes and drafts a PR title, description, and risk report. Never commits, pushes, or opens PRs.
allowed-tools: Bash, Read, Grep
disable-model-invocation: true
---

You are reviewing staged changes before a Pull Request is opened.

1. Run `git diff --cached` and `git status` to see exactly what is staged.
2. Report any of the following if present: secrets or credential-shaped
   strings, debug print/echo statements, TODO/FIXME left in code, a diff
   that mixes unrelated concerns, or a change with no corresponding notes.
3. Draft a PR title that starts with a short word like `feat:` or `fix:`
   telling the reader what kind of change this is, and a 3-5 sentence PR
   description explaining what changed and why.
4. Never run `git commit`, `git push`, or `gh pr create`. Never edit files.
   Your output is a draft for a human to review and use.
```

Run it with `/pr-ready`.

### Evidence

#### Screenshot 5 — `SKILL.md` frontmatter showing `allowed-tools: Bash, Read, Grep` (no `Write`) and `disable-model-invocation: true`

![alt text](screenshots\sc5-T4-ass6-week4.png)

---

#### Screenshot 6 — `/pr-ready` output while the risky file is still staged, showing it flagged the secret and/or debug statement

![alt text](screenshots\sc6-T4-ass6-week4.png)

---

### Notes

**1. Why does `/pr-ready` have `Bash` and `Read` but not `Write`?**

🛡️ Safety & Mutability: Why Write is Omitted
The entire purpose of a PR readiness check is verification, not modification.

Preventing Race Conditions & Side Effects: If the agent had Write access, it could accidentally modify files, auto-format code in unexpected ways, or edit staged files right before submission—changing the exact state you intended to review.

Separation of Concerns: Writing or modifying code belongs to the development phase. Once you trigger /pr-ready, the agent’s job shifts strictly to auditing and gatekeeping.

Preventing Unintended Staging: Omitting Write ensures the agent cannot create untracked files or alter files in the working directory that might unexpectedly break the Git staging index or trigger pre-commit hook failures.

---

**2. The pre-commit hook and `/pr-ready` both looked at the same staged diff. Did they flag the same things? What did one catch that the other didn't?**

What /pr-ready caught (that pre-commit couldn't):
Contextual & Intent Analysis: Beyond regex matching, /pr-ready uses an LLM/agentic process to read and understand the meaning and context of changes (e.g., recognizing that a credential might be part of a test/mock script vs. production code).

Broader Repository Health: /pr-ready checked high-level PR readiness factors that pre-commit ignores entirely—such as:

Whether the PR title and description accurately summarize the changes.

Checking for missing documentation or outdated README.md sections.

Identifying untracked files or overall repository hygiene across the entire working tree (not just the staged index).

Reviewing code quality, readability, and adherence to assignment requirements.

---

# Task 5 — Fix the Issues and Re-Verify

## Goal

Remove the secret and debug statement, then prove both gates now pass clean.

### Evidence

#### Screenshot 7 — `git commit` succeeding after the fix (no BLOCKED message)

![alt text](screenshots\sc7-T5-ass6-week4.png)

---

#### Screenshot 8 — Second `/pr-ready` run showing a clean risk report and a drafted PR title + description

![alt text](screenshots\sc8-T5-ass6-week4.png)

---

### Notes

**1. What exactly did you change to satisfy the pre-commit hook?**

Removed Hardcoded Credentials:

What was removed: AWS_ACCESS_KEY_ID=AKIA_REDACTED_KEY

Why: The pre-commit hook uses a regular expression  to scan staged files for potential AWS access keys. Deleting the hardcoded AKIA... string eliminated the exact string pattern that was triggering the hook's regex scan.

Removed Debug Output of Tokens:

What was removed: echo "DEBUG: token is $AWS_ACCESS_KEY_ID"

Why: Printing security tokens—even via environment variables—to stdout/logs is a security risk (credential leakage). Replacing it with a safe confirmation message (echo "Notification script executed successfully.") ensures sensitive values are never logged to console outputs.

---

# Task 6 — Open the Pull Request Using the AI Draft

## Goal

Push your branch and open a real Pull Request, using `/pr-ready`'s drafted title and description as your starting point — read it critically and edit before you use it.

**Important:** Open this Pull Request with base repository set to **your own fork** — not the shared upstream `pravinmishraaws/devops-micro-internship-pravinmishra` repository. This assignment's hook and skill files are your own practice work, not a change meant for the shared class repo.

### Evidence

#### Screenshot 9 — Your Pull Request showing the base repository is your own fork, plus the title and description, with the `/pr-ready` draft visible for comparison (paste it in the PR conversation or your notes below)
![alt text](screenshots\sc9.1-T6-ass6-week4.png)


![alt text](screenshots\sc9.2-T6-week4.png)


---

#### PR Link

https://github.com/pravinmishraaws/devops-micro-internship-interviews/pull/357

---

### Notes

**1. What, if anything, did you edit in the AI's drafted PR description before using it? Why?**

If you edited the AI's drafted PR description:
What was edited:

Added specific details about resolving the pre-commit hook secret block on scripts/notify.sh.

Updated the checklist items to explicitly confirm that screenshots and assignment notes were included.

Tweaked the tone to keep it concise and focused strictly on the assignment deliverables.

Why:

Accuracy & Verification: AI-generated PR descriptions often summarize general code changes well, but they can miss specific context—like why a fake credential was temporarily introduced and then cleaned up to satisfy security checks.

Human Oversight: Editing ensures the PR description directly aligns with the course grading rubrics and clearly highlights completed tasks for reviewers without unnecessary AI filler.

---

**2. If you had blindly copy-pasted the AI's draft without reading it, what could go wrong?**

AddAI tools often infer intent or assume standard practices, which can lead them to invent details that never happened.

The Risk: The draft might claim you "added unit tests," "updated API documentation," or "fixed a bug in auth.js" when you did no such thing.

Impact: A code reviewer will search for those non-existent changes, waste time, and lose trust in your PR.

---

**3. Why does this PR need to target your own fork instead of the shared upstream repository?**

Add your answer here.Targeting your own fork rather than the shared upstream repository is standard practice in both team development and course assignments. Here is why:

1. 🛑 Permission & Write Access Boundaries
Upstream is protected: You generally do not have direct write or push access to the main upstream repository (the original project template or organization repo).

Forks are personal: Your fork lives under your personal GitHub account (your-username/repo-name), giving you full admin rights to push branches, open pull requests, and experiment safely without affecting the core project.

---

# Task 7 — Map the Workflow to the Agentic Loop

## Goal

Explain this assignment's workflow using the same Gather → Analyze → Human Act → Verify structure from Week 3.

### Notes

**1. Which step(s) represent Gather?**

n the Agentic Loop framework (Gather $\rightarrow$ Action $\rightarrow$ Verify), the Gather phase encompasses any step where information is read and collected from the system without modifying its state.Based on your course setup, the following step(s) represent Gather:1. Running the System Telemetry & Triage ScriptsExecuting any read-only diagnostic or audit commands (such as your triage.sh script, git status, git diff, systemctl status, or df -h).Why: These commands retrieve real-time state data (memory usage, service health, git staging index, file sizes) without making structural changes.

---

**2. Which step(s) represent Analyze?**

Identifying Root Cause or Pattern Matching
Comparing the gathered facts against rules, patterns, or heuristics to find the exact problem:

Matching the staged code against regex patterns in the pre-commit hook (e.g., detecting AKIA[0-9A-Z]{16} in scripts/notify.sh).

Evaluating high disk usage or a crashed Nginx service from server logs.

Spotting missing documentation or untracked screenshots in the repository.

---

**3. Which step is Human Act, and why must a human — not Claude — run `git commit`, `git push`, and open the PR?**

Which step is Human Act?
Human Act is the decision-and-execution phase where a human operator steps in to authorize and apply persistent changes to the codebase, branch history, or remote repository after reviewing the AI's recommendations.

🛡️ Why must a human — not Claude — run git commit, git push, and open the PR?
1. Accountability & Legal/Code Ownership
Every Git commit attaches an author name and email to the repository's permanent history.

Sign-off & Responsibility: A human must explicitly accept ownership of the code changes. If an AI agent autonomously commits and pushes code, accountability for bugs, security vulnerabilities, or licensing issues becomes untraceable.

Audit Compliance: In production environments, human sign-off ensures that a real team member reviewed and accepted responsibility for what enters the codebase.

---

**4. Which step is Verify?**
In the Agentic Loop framework (Gather $\rightarrow$ Analyze $\rightarrow$ Action $\rightarrow$ Verify), Verify is the final phase where you validate that the action taken actually solved the problem without introducing new issues or breaking existing functionality.The following step(s) represent Verify:1. Re-running the Pre-Commit Hook (git commit)Executing git commit after editing scripts/notify.sh to confirm that the hook runs cleanly without raising a "BLOCKED" error.Why: This proves that removing the hardcoded AKIA string successfully satisfied the secret-scanner regex rule.2. Checking git status and git diffInspecting the repository state after staging the updated script to ensure:The hardcoded credential and debug echo are completely gone.No temporary debug files, logs, or unintended edits were left behind in the workspace.3. Re-running /pr-ready (or Diagnostic Triage)Triggering the PR readiness check again to verify that all repository health checks pass before opening the Pull Request.4. Reviewing the GitHub PR Diff PageInspecting the file changes on GitHub right before hitting "Create Pull Request" to confirm that the remote branch contains the exact cleaned version of the code.Key Rule of Verify: Any step where you re-check, re-test, or audit the system state to confirm that your fix worked as intended is part of Verify.

---

**5. In one or two sentences: why do you need *both* the fixed-rule pre-commit hook and the AI skill? Isn't one enough?**

You need both because they complement each other's inherent blind spots: the fixed-rule pre-commit hook provides an instant, zero-cost, deterministic block against known catastrophic errors (like standard AWS key formats), while the AI skill provides context-aware reasoning to evaluate code quality, detect complex or non-standard secrets, and assess overall PR readiness. Neither is sufficient on its own—the fixed rule lacks semantic context, and the AI skill lacks the strict, instant binary enforcement needed at the local git commit boundary.

---

# Task 8 — LinkedIn Post

## Goal

Publish a LinkedIn post summarizing what you built and what you learned about combining fixed-rule safety checks with AI-assisted review.

### Evidence

#### LinkedIn Post URL

https://www.linkedin.com/posts/taysir-ouaslati-9b6527a3_building-an-ai-assisted-git-safety-net-why-share-7486092935848185856-JaJD/?utm_source=share&utm_medium=member_desktop&rcm=ACoAABX4AtoB0tpceeC8Jnqozhzdi2ViZ02bFHk

---

## Key Learnings

Add 3-5 bullet points on what you learned this week.

- Building a Two-Tiered Git Safety Net: Learned how combining deterministic, regex-based pre-commit hooks (for instant hard-blocking of sensitive credentials like AWS keys) with contextual AI tools provides a much more robust local DevSecOps defense than relying on either tool alone.
- Mastering the Agentic Loop: Applied the Gather $\rightarrow$ Analyze $\rightarrow$ Action $\rightarrow$ Verify operational pattern to systematically gather system context, diagnose root causes, execute safely, and verify that fixes pass all checks before merging.
- Understanding Human-in-the-Loop Governance: Identified why state-changing Git operations (git commit, git push, and PR creation) must remain under human control to ensure proper code attribution, accountability, and safety guardrails.Managing Fork Workflows & Repository Hygiene: Practiced managing feature branches, resolving pre-commit blocks safely using environment variables, and properly opening PRs against your personal fork rather than the upstream project template.


---

# Submission Instructions

- Ensure `hooks/pre-commit` and `.claude/skills/pr-ready/SKILL.md` are committed to your GitHub repository
- Add all required screenshots to your submission
- All written answers must be in your own words
- Do not use a real secret or credential anywhere in your submission — the fake key in Task 1 is intentional and must stay clearly fake
- Open your Pull Request against your own fork, not the shared upstream repository
- Push your final changes to your forked repository
- Include your PR link and LinkedIn post URL

---

## GitHub Repository URL

Paste your forked repository URL here:

https://github.com/siraa/devops-micro-internship-interviews

---

# Completion Checklist

- [ ] Branch `feature/ai-pr-ready` created with a staged file containing a fake secret and a debug statement
- [ ] `hooks/pre-commit` created and tracked in the repo (not only in `.git/hooks/`)
- [ ] `core.hooksPath` configured to point at `hooks/`
- [ ] Pre-commit hook shown blocking the risky commit
- [ ] `.claude/skills/pr-ready/SKILL.md` created with correct `allowed-tools` (no `Write`) and `disable-model-invocation: true`
- [ ] `/pr-ready` run against the risky diff and shown flagging issues
- [ ] Risky file fixed; `git commit` succeeds cleanly
- [ ] `/pr-ready` re-run showing a clean report and drafted PR title/description
- [ ] Pull Request opened using the AI draft as a starting point, with your own fork as the base repository (not upstream), PR link included
- [ ] Agentic Loop mapping (Task 7) completed in your own words
- [ ] LinkedIn post published and URL submitted
- [ ] All required screenshots added
- [ ] GitHub repository URL provided

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
