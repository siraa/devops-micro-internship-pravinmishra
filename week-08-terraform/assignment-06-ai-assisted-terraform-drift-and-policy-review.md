# Assignment 6 — AI-Assisted Terraform Drift and Policy Review

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Student Details

**Full Name:** Tayssir Oueslati  
**GitHub Repository/Folder URL:** https://github.com/siraa/devops-micro-internship-pravinmishra

---

## Purpose

Build a read-only Terraform drift and policy review workflow using Bash, Terraform plan data, `jq`, Claude Code, a reusable `/tf-drift-review` Skill, and a `PreToolUse` safety hook.

The workflow must follow this pattern:

```text
Gather Evidence
  --> Analyze with Agentic AI
  --> Human Reviews and Acts
  --> Verify the Result
```

The `/tf-drift-review` Skill and `tf-drift-check.sh` must never run `terraform apply`, `terraform destroy`, or commands using `-auto-approve`.

---

# Task 1 — Confirm the Clean Baseline and Create the Workspace

## Goal

Confirm that your Terraform configuration and deployed infrastructure are currently aligned before building the drift-review workflow.

## Evidence

### Screenshot 1 — Clean Terraform Plan

Add a screenshot of `terraform plan` showing no pending changes.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 2 — Assignment Workspace

Add a screenshot of the folder structure showing `AI Assignment/`, `reports/`, and the Terraform project.

![alt text](screenshots\sc1-T0-ass1-week8.png)

## Questions

### 1. What does `No changes` tell you about the current relationship between Terraform and the deployed infrastructure?

It means that Terraform's configuration matches the currently deployed infrastructure. Terraform has detected no differences that require creating, modifying, or deleting resources.

### 2. Why is a clean baseline important before introducing a test change?

A clean baseline confirms that the infrastructure is stable before testing. It makes it easier to identify the exact impact of the test change and verify that the infrastructure returns to its original state afterward.

---

# Task 2 — Create Project Context and Safety Rules in `CLAUDE.md`

## Goal

Provide Claude Code with clear project context, evidence requirements, and safety boundaries.

## Evidence

### Screenshot 3 — Project Context and Safety Rules

Add a screenshot of `CLAUDE.md` open in VS Code showing the Project Overview, Review Workflow, Safety Rules, and Output Rules.

![alt text](screenshots\sc1-T0-ass1-week8.png)

## Questions

### 1. Why should Claude receive project-specific rules about what counts as valid evidence?

Claude needs project-specific evidence rules to distinguish reliable information from assumptions. This helps it analyze Terraform plans and infrastructure changes accurately and provide recommendations based on actual evidence.

### 2. Why must the human remain responsible for running `terraform apply`?

Because terraform apply makes real changes to cloud infrastructure, including creating, modifying, or deleting resources. Human approval ensures that the proposed changes are reviewed before execution and prevents unintended infrastructure modifications.

### 3. Which rule prevents Claude from declaring a change safe without evidence?

The evidence-based decision rule requires Claude to inspect the Terraform plan and supporting information before making a safety assessment. If sufficient evidence is unavailable, Claude must report that the change cannot be verified as safe rather than making assumptions.



# Task 3 — Build the Terraform Drift and Policy Check Script

## Goal

Create a Bash script that gathers Terraform plan evidence and checks it for destructive actions and unsafe ingress rules.

## Evidence

### Screenshot 4 — Script Variables and Checks Array

Add a screenshot of the top section of `tf-drift-check.sh` showing the variables and `checks` array.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 5 — Destructive-Action and Open-Ingress Checks

Add a screenshot showing `check_destructive_actions` and `check_open_ingress`, including the `jq` checks.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

# Task 4 — Run the Script Against the Clean Baseline

## Goal

Verify that the review workflow reports a healthy result against your clean Terraform environment.

## Evidence

### Screenshot 7 — Healthy Baseline Report

Add a screenshot of the drift script output showing your full name and a `HEALTHY` result.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 8 — Baseline Script Exit Code

Add a screenshot showing the captured script exit code `0`.

![alt text](screenshots\sc1-T0-ass1-week8.png)

## Questions

### 1. What is the Overall Status of your baseline?

The Overall Status of my baseline is HEALTHY, indicating that the Terraform infrastructure is stable and no issues were detected.

### 2. Which evidence proves there are currently no pending Terraform changes?

The Terraform plan output shows No changes, confirming that the current Terraform configuration matches the deployed infrastructure and no resources need to be created, modified, or deleted.

### 3. Was `reports/tfplan.json` created? Explain why or why not.
No. The file was not created because the baseline check detected no infrastructure changes requiring further plan analysis. There was no pending change to review in the Terraform plan. 



# Task 5 — Create and Run the `/tf-drift-review` Claude Code Skill

## Goal

Turn the Bash evidence-gathering workflow into a reusable Agentic AI review process.

## Evidence

### Screenshot 9 — `/tf-drift-review` Skill Configuration

Add a screenshot of `SKILL.md` showing the frontmatter, allowed tools, and safety rules.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 10 — Clean Agentic AI Review

Add a screenshot of `/tf-drift-review` showing the clean `HEALTHY` result.

![alt text](screenshots\sc1-T0-ass1-week8.png)

## Questions

### 1. Why does this Skill have `Bash`, `Read`, and `Grep`, but not `Write`?

These tools allow Claude to execute read-only checks, inspect files, and search for relevant information without modifying project files. Excluding Write helps prevent accidental changes to infrastructure configuration.

### 2. Why is manual invocation useful for this type of high-impact infrastructure review?

Manual invocation ensures that the user decides when the infrastructure review starts. This provides human oversight and prevents unexpected or unnecessary execution of the review workflow.

### 3. Which part of the workflow is deterministic Bash automation?

The Bash script performs deterministic tasks such as running Terraform commands, collecting plan information, checking exit codes, and generating structured reports.

### 4. Which part requires Claude's reasoning?

Claude interprets the collected evidence, analyzes proposed infrastructure changes, identifies potential risks, and explains whether the changes appear safe or require further review.

### 5. Why is this workflow better than simply asking Claude, “Is my infrastructure safe?”

This workflow is better because Claude bases its assessment on actual Terraform output and structured evidence rather than assumptions. It also separates automated evidence collection from AI reasoning and keeps the final approval and execution of infrastructure changes under human control.



# Task 6 — Introduce a Controlled Difference and Detect It

## Goal

Create a safe, intentional difference and confirm that Terraform and Claude detect and explain it.

## Evidence

### Screenshot 11 — Controlled Difference

Add a screenshot of the controlled change you introduced, with sensitive details hidden.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 12 — Detected Difference and Risk Assessment

Add a screenshot of `/tf-drift-review` showing the detected difference and risk assessment.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 13 — Detected Drift Report

Add a screenshot of `drift-detected-report.txt` showing your full name and the `WARN` or `FAIL` result.

![alt text](screenshots\sc1-T0-ass1-week8.png)

## Questions

### 1. What change did you introduce?

I introduced a controlled change to the Terraform configuration by modifying [INSERT THE RESOURCE OR CONFIGURATION YOU CHANGED] to test whether the review workflow could detect and analyze pending infrastructure changes.

### 2. Was it true infrastructure drift or a Terraform configuration change?

It was a Terraform configuration change because I manually modified the configuration file. It was not true infrastructure drift caused by an external change to the deployed resources.

### 3. What Terraform plan evidence proves that a change is pending?

It was a Terraform configuration change because I manually modified the configuration file. It was not true infrastructure drift caused by an external change to the deployed resources.

### 4. Was the action an update, deletion, replacement, or security-rule change?

The planned action was [INSERT THE ACTUAL ACTION: UPDATE, DELETE, REPLACEMENT, OR SECURITY-RULE CHANGE], as shown in the Terraform plan.

### 5. What did Claude recommend?

Claude analyzed the Terraform plan, identified the proposed change and its potential impact, and provided a recommendation. It advised reviewing the change carefully before applying it.

### 6. Why should you review the recommendation before taking action?

The recommendation must be reviewed to ensure that the proposed change is necessary, safe, and consistent with the project requirements. Terraform changes can affect running infrastructure, introduce security risks, or cause service disruption. Human approval helps prevent unintended consequences.

---

# Task 7 — Add a `PreToolUse` Hook to Block Unsafe Apply Attempts

## Goal

Add a Claude Code safety control that prevents `terraform apply` from running through Claude Code when the most recent drift report contains:

```text
Overall Status: FAIL
```

## Evidence

### Screenshot 14 — `PreToolUse` Safety Hook

Add a screenshot of `.claude/settings.json` showing the `PreToolUse` safety hook.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 15 — Blocked Apply Attempt

Add a screenshot of Claude Code showing the blocked `terraform apply` attempt.

![alt text](screenshots\sc1-T0-ass1-week8.png)

## Questions

### 1. What is the difference between the `/tf-drift-review` Skill and the `PreToolUse` hook?

The /tf-drift-review Skill analyzes Terraform plans, collects evidence, identifies potential risks, and provides recommendations. The PreToolUse hook acts as a safety mechanism that checks commands before execution and prevents unauthorized or unsafe infrastructure changes.

### 2. Which component performs analysis?

The /tf-drift-review Skill performs the analysis by examining Terraform plan output and identifying potential infrastructure risks.

### 3. Which component enforces the safety gate?

The PreToolUse hook enforces the safety gate by checking high-impact commands before they are executed and blocking them when the required safety conditions are not met.

### 4. Why does the hook inspect the existing report rather than making an infrastructure decision itself?

The existing report contains the evidence and analysis produced by the Skill. The hook uses this information to verify whether the required review has been completed, keeping the analysis separate from command enforcement.

### 5. Why is a deterministic guard useful for high-impact commands?

A deterministic guard applies consistent safety rules before high-impact commands are executed. It reduces the risk of accidental infrastructure changes and ensures that commands such as terraform apply require the appropriate human review and approval.

---

# Task 8 — Resolve the Difference and Verify the Final State

## Goal

Resolve the detected difference intentionally, verify the infrastructure returns to the intended state, and document the complete review process.

## Evidence

### Screenshot 16 — Human-Reviewed Resolution

Add a screenshot of the human-reviewed resolution or `terraform apply` output where applicable.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 17 — Final Healthy Review

Add a screenshot of the final `/tf-drift-review` showing `HEALTHY`.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 18 — Saved Reports

Add a screenshot of `ls -lah reports` showing both:

- `drift-detected-report.txt`
- `resolved-report.txt`

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Notes

Explain why this workflow needs both a fixed-rule hook that blocks `apply` outright and an AI skill that explains the risk in plain language — why isn't one of the two enough on its own?

They serve two different purposes:

The fixed-rule hook provides a hard safety boundary. It blocks terraform apply regardless of what the AI thinks or says. This prevents the agent from accidentally making infrastructure changes.
The AI skill provides context and explanation. It can explain what the proposed changes mean, identify potential risks, and help the human decide whether the plan is safe to apply.

One alone is not enough:

Hook only: safe, but it gives little help understanding why the change is risky or what needs to be reviewed.
AI skill only: informative, but the AI could misunderstand a situation or fail to recognize a dangerous change. There would be no guaranteed technical block.

Together, they provide enforced safety + human-readable reasoning: the hook prevents the unsafe action, while the AI helps the human understand and make the final decision.

---

# Submission Instructions

- Complete Tasks 1–8 in sequence.
- Include Screenshots 1–19 exactly as specified.
- Answer every question under Tasks 1–8 in your own words.
- Complete all seven sections of the Terraform Drift Review Summary.
- Include the GitHub repository/folder URL containing the assignment files.
- Include your full name in the required reports and screenshots.
- Include the LinkedIn post URL and a screenshot of the published LinkedIn post.
- Do not expose access keys, passwords, tokens, account IDs, private keys, Terraform secrets, or other sensitive information.
- Review all screenshots carefully and hide or redact sensitive details where necessary.

---

# Completion Checklist

- [ ] Confirmed a clean Terraform baseline
- [ ] Created the required assignment workspace
- [ ] Created or updated `CLAUDE.md`
- [ ] Added project context and safety rules
- [ ] Created `tf-drift-check.sh`
- [ ] Added my full name to the report
- [ ] Validated the Bash script
- [ ] Made the script executable
- [ ] Used `terraform plan -detailed-exitcode`
- [ ] Used Terraform plan JSON
- [ ] Used `jq` to inspect destructive actions
- [ ] Used `jq` to inspect unsafe ingress
- [ ] Confirmed the baseline returns `HEALTHY`
- [ ] Created `/tf-drift-review`
- [ ] Restricted the Skill to appropriate tools
- [ ] Confirmed the Skill remains read-only
- [ ] Confirmed the Skill never runs `terraform apply`
- [ ] Confirmed the Skill never runs `terraform destroy`
- [ ] Introduced a controlled detectable difference
- [ ] Correctly identified whether it was true drift or a configuration change
- [ ] Saved `drift-detected-report.txt`
- [ ] Added the `PreToolUse` safety hook
- [ ] Verified the hook blocks `terraform apply` when the report is `FAIL`
- [ ] Reviewed the Terraform evidence before resolving the change
- [ ] Performed any infrastructure-changing action manually
- [ ] Ran the drift review again after resolution
- [ ] Confirmed the final status is `HEALTHY`
- [ ] Saved `resolved-report.txt`
- [ ] Completed `drift-review-summary.md`
- [ ] Mapped the workflow to `Gather --> Analyze --> Human Act --> Verify`
- [ ] Included all 19 numbered screenshots
- [ ] Answered all required questions
- [ ] Published the required LinkedIn post
- [ ] Added the LinkedIn post URL and screenshot
- [ ] Included the GitHub repository/folder URL
- [ ] Confirmed that no sensitive information is exposed

---

*This submission is part of the DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
