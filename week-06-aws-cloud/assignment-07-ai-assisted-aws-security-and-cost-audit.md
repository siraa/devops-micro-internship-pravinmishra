# Assignment 7 — AI-Assisted AWS Security and Cost Audit

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build a read-only Bash script that audits the AWS resources you deployed earlier this week — your S3 static site, EC2 instance(s), security groups, RDS database, and EBS volumes — for common security and cost misconfigurations.

You will then connect that script to Claude Code as a reusable `/aws-audit` skill that explains what it found and recommends a fix, without ever making the fix itself.

Finally, you will find a real misconfiguration in your own account, apply the fix yourself, and prove it worked with a second audit run.

---

# Task 1 — Confirm Your AWS Resources and Set Up Your Workspace

## Goal

Confirm your AWS CLI is authenticated and can see the S3 bucket, EC2 instance(s), and RDS instance you built earlier this week, then create a workspace folder for this assignment.

### Evidence

#### Screenshot 1 — Output of `aws s3 ls`, the EC2 instance table, and the RDS instance table (blur the Account ID if visible)
![alt text](screenshots\sc1-T1-ass7-week6.png).

---

#### Screenshot 2 — Output of `pwd` and `find . -maxdepth 4 -type d | sort`

![alt text](screenshots\sc2-T1-ass7-week6.png).



---

### Notes You Must Write (Very Important)

**1. Which resources from this week's earlier assignments did you see in the listings?**

Based on the environment exploration and AWS CLI queries executed across this week's assignments, the following resources were identified in the account listings:

Amazon RDS Databases:

book-review-db-1 (Status: available) — MySQL database instance provisioned for the book review application stack.

**2. Why must you confirm your resources exist before writing an audit script against them?**

Here are the core reasons why confirming resource existence is a critical prerequisite before writing or running an audit script:

Target Accuracy & Identifier Validation: AWS resource identifiers (Instance IDs, Subnet IDs, DB Instance Identifiers) change across lab reinstantiations, environment resets, or account redeployments. Verifying active resources ensures your script targets real, operational infrastructure rather than hardcoded or obsolete IDs.

Prevents Runtime Script Crashes: Executing CLI commands or SDK functions against non-existent resources triggers API exceptions (such as ResourceNotFoundException or DBInstanceNotFoundException). Unhandled errors will cause the script to exit prematurely before completing the audit cycle.

Eliminates False Positives / Skewed Compliance Reporting: An audit script querying a missing or terminated resource will return Null or Empty data. If unvalidated, the script might misinterpret missing infrastructure as a security compliance failure rather than a targeting error, ruining the integrity of your security audit reports.

Reduces API Throttling & Operational Noise: Iterating over non-existent or misconfigured targets generates unnecessary API call retries, leading to AWS API rate-limiting (throttling) and cluttering execution logs with unhelpful error messages.

Establishes an Accurate Audit Baseline: Confirming actual resource presence allows you to write targeted conditional logic in your script (e.g., verifying StorageEncrypted: true or MultiAZ: true specifically on active RDS instances like book-review-db-1).

---

# Task 2 — Define Safety Rules in CLAUDE.md

## Goal

Create a `CLAUDE.md` in your workspace that tells Claude the audit script is read-only, that it must never run a command that creates, modifies, or deletes an AWS resource, and that any remediation must be recommended, never executed automatically.

### Evidence

#### Screenshot 3 — `CLAUDE.md` open in VS Code showing all four sections

![alt text](screenshots\sc3-T2ass7-week6.png).

---

### Notes You Must Write (Very Important)

**1. Why should Claude never be given permission to run `revoke-security-group-ingress` itself, even if the fix is obviously correct?**

Confirming that your resources exist before writing an audit script against them is a critical step for several technical reasons:

Ensures Target Accuracy: AWS resource identifiers (Instance IDs, Subnet IDs, DB Instance Identifiers) change whenever environments are redeployed, reset, or reinstantiated. Confirming active resources ensures your script targets real, operational infrastructure rather than hardcoded, stale, or obsolete IDs.

Prevents Runtime Script Crashes: Executing CLI commands or SDK queries against non-existent resources triggers API exceptions (such as ResourceNotFoundException or DBInstanceNotFoundException). Without pre-verification, these unhandled errors cause scripts to exit prematurely before completing the audit cycle.

Eliminates False Positives & Misleading Reports: Querying a missing or terminated resource returns Null or empty outputs. Unvalidated scripts often misinterpret this missing data as a security compliance failure rather than a targeting error, producing inaccurate security reports.

Avoids API Rate Limiting & Log Noise: Repeatedly querying non-existent endpoints leads to unnecessary API retries, causing AWS API throttling (rate-limiting) and cluttering execution logs with error noise.

Establishes an Accurate Audit Baseline: Verifying active infrastructure allows you to write precise conditional logic tailored to your specific deployment (e.g., explicitly checking StorageEncrypted: true or PubliclyAccessible: false on active RDS instances like book-review-db-1).

**2. Which rule prevents Claude from claiming a finding that the report does not support?**

The rule that prevents Claude from claiming a finding that the report does not support is the Evidence Grounding Rule (often referred to in prompt engineering and project instructions as the Strict Evidence / No Extrapolation Rule).

Specifically, within standard system prompts and project guidelines (like CLAUDE.md), this rule is enforced through three key constraints:

Strict Factuality (No Hallucination): Claude must only state findings, statuses, or compliance results that are directly backed by the raw CLI/API output contained in the audit report.

Prohibition of Assumptions: If data for a specific resource or check is missing, ambiguous, or incomplete in the report, Claude is forbidden from assuming a pass/fail status or inventing details.

Explicit Citation Requirement: Claude must reference the specific JSON/table entry or key-value pair from the audit execution log to substantiate every claim made in the summary or finding report.

In short, if a finding cannot be traced back to an explicit line of output in the audit report, Claude is strictly prohibited from including or claiming it.

---

# Task 3 — Plan the Audit with Claude Code

## Goal

Ask Claude Code to propose a read-only audit plan covering five checks — S3 public-access settings, security groups open to the whole internet on SSH and MySQL ports, RDS public accessibility, and EBS volume encryption — without creating or editing any file yet.

### Evidence

#### Screenshot 4 — Claude Code showing the five-check plan

![alt text](screenshots\sc4.1-T3-ass7-week6.png).

---

### Notes You Must Write (Very Important)

**1. Which part of this task represents the Gather phase?**

In the context of an automated audit or security script lifecycle, the Gather phase is represented by executing the read-only AWS CLI / API commands to inspect and collect the current configuration state of your resources.

Specifically, in your AWS audit task, the Gather phase consists of running the read-only inspection commands to fetch raw status data:

S3 Check: Running aws s3control get-public-access-block (or aws s3api get-public-access-block) to retrieve the public ACL settings.

EC2 SSH Check: Running aws ec2 describe-security-groups filtered on port 22 to fetch ingress rules.

EC2 MySQL Check: Running aws ec2 describe-security-groups filtered on port 3306 to fetch database ingress rules.

RDS Check: Running aws rds describe-db-instances to inspect the PubliclyAccessible parameter.

EBS Check: Running aws ec2 describe-volumes to inspect the Encrypted state of all attached storage volumes.

**2. Did every proposed command start with `describe-`, `get-`, or `list-`? Why does that matter?**

Yes, every proposed command started with describe-, get-, or list-.

Here is why that specific prefix convention matters in AWS security and auditing:

Guarantees Read-Only & Non-Destructive Operations: AWS API actions that begin with describe- (EC2/RDS), get- (S3/IAM), or list- (S3/Resource Groups) are strictly read-only inspection calls. They query and return the current state of infrastructure without modifying, interrupting, or deleting any active resources.

Adheres to the Principle of Least Privilege (PoLP): Security audit scripts and service roles should only be granted permission actions matching these prefixes (e.g., ec2:Describe*, s3:Get*, rds:Describe*). Restricting privileges prevents an auditor or an automated script from accidentally altering configurations or terminating production infrastructure.

Prevents Accidental Downtime or Data Loss: Commands starting with prefixes like create-, put-, update-, modify-, or delete- make active changes to your cloud environment. Ensuring an audit script exclusively uses describe-, get-, and list- guarantees that running the script poses zero operational risk to your live workloads.

---

# Task 4 — Build the AWS Audit Script

## Goal

Write a Bash script that runs the five checks from Task 3 using only read-only AWS CLI calls, writes a PASS/WARN/FAIL report to a file, and exits with a different code depending on the overall result.

Make it executable and confirm it has no syntax errors.

### Evidence

#### Screenshot 5 — Top section of `aws-audit.sh` showing the variables and the checks array

![alt text](screenshots\sc5-T4-ass7-week6.png).

---

#### Screenshot 6 — One check function (for example `check_ssh_open_to_world`) showing the AWS CLI call and conditional

![alt text](screenshots\sc12-T5-ass4-week6.png).

---

#### Screenshot 7 — Output of `bash -n scripts/aws-audit.sh` and `ls -l scripts/aws-audit.sh`

![alt text](screenshots\sc5-T4-ass7-week6.png).

---

### Notes You Must Write (Very Important)

**1. What is stored in the checks array, and how does the loop use it?**

e array holds a list of the function names that perform the specific AWS CLI checks:

**2. Why does every AWS CLI call in this script use `--query` and `--output text` instead of parsing raw JSON?**

Using --query and --output text allows AWS to filter and format the response on its servers before sending back a plain, single-line text value, avoiding complex local JSON parsing in Bash.

No External Dependencies (jq): Bash cannot parse JSON natively. Without --query, your script would rely on third-party tools like jq or Python being installed on the runner machine.

Simplifies Bash Logic: Instead of capturing multi-line JSON blocks and parsing them with grep or sed, --output text returns clean, single-line string or integer output (e.g., true, false, 0) that can be used directly in Bash if conditions.

Server-Side Efficiency: The filtering happens on AWS servers via JMESPath before returning the payload, reducing network bandwidth and keeping the execution fast.

**3. Why does the script use different exit codes for HEALTHY, WARN, and FAIL?**

The script uses distinct exit codes (typically 0 for HEALTHY/PASS, 1 for WARN, and 2 for FAIL) to communicate the execution result to the operating system and downstream automation tools.

Automated CI/CD Pipeline Integration: CI/CD runners (like GitHub Actions, GitLab CI, or Jenkins) rely on process exit codes to determine whether a pipeline stage passed or failed. An exit code of 0 lets the build proceed, whereas 1 or 2 immediately triggers a pipeline failure or alert.

Programmable Decision Making: Downstream scripts or orchestrators can programmatically evaluate $? (the exit status of the last executed command) to branch their logic without having to parse text strings or report files.

Distinguishing Non-Critical Warnings from Severe Failures: Using separate non-zero exit codes allows automated workflows to differentiate between non-blocking configuration warnings (Code 1) and severe security policy violations that require halting deployment immediately (Code 2).

---

# Task 5 — Run the Baseline Audit

## Goal

Run the script against your live AWS account and capture the current state before making any changes.

### Evidence

#### Screenshot 8 — Output of `./scripts/aws-audit.sh` showing your Full Name and all five checks

![alt text](screenshots\sc5-T4-ass7-week6.png).

---

#### Screenshot 9 — Output showing the captured exit code and final summary

![alt text](screenshots\sc5-T4-ass7-week6.png).

---

### Notes You Must Write (Very Important)

**1. What is the overall status of your baseline audit?**

PASS4S3 public access, SSH port 22 access, MySQL port 3306 access, and RDS public accessibility checks all passed.WARN18 EBS volume(s) are not encrypted at rest.FAIL0No critical security failures remaining.

**2. Did any check return FAIL or WARN? If so, which one, and what evidence did it show?**

Yes. While no checks returned FAIL, one check returned a WARN status.

Details & Evidence
Status: [WARN]

Check Name: EBS Volume Encryption Check

**3. If every check passed, what does that tell you about the security posture of your account so far?**

If every check passed (100% PASS), it tells you that your account meets the specific baseline security rules evaluated by your audit script—meaning no public S3 ACLs, no open administrative/database ports to 0.0.0.0/0, isolated RDS instances, and encrypted EBS volumes.

However, from a holistic Cloud Security posture perspective:

It proves compliance against defined rules, not total security: An audit script only tests what it is programmed to inspect. Passing 5 automated checks does not guarantee immunity from other misconfigurations.

Potential blind spots remain: Blind spots like weak IAM policies (e.g., overly permissive wildcards *), unrotated access keys, missing MFA on root/IAM users, disabled AWS CloudTrail logging, or unpatched OS vulnerabilities inside EC2 instances would still go unnoticed unless explicit check functions are added for them.

It confirms a strong defense-in-depth foundation: It verifies that fundamental "low-hanging fruit" vulnerabilities—the most common attack vectors for automated scanners on the public internet—have been effectively remediated.

---

# Task 6 — Build and Run the /aws-audit Skill

## Goal

Turn the script into a Claude Code skill named `/aws-audit` that runs the script, reads the report, and explains every finding along with its estimated cost or security risk — with tool access restricted so it can never modify your AWS account.

### Evidence

#### Screenshot 10 — `SKILL.md` showing the frontmatter, tool restrictions, and safety rules

![alt text](screenshots\sc10.1-T10-ass7-week6.png).

---

#### Screenshot 11 — `/aws-audit` output showing findings, cost/risk impact, and a recommended remediation command (or a clean report if your baseline passed everything)

![alt text](screenshots\sc10.2-T6-ass7-week6.png).

---

### Notes You Must Write (Very Important)

**1. Why does this skill have Bash, Read, and Grep, but not Write?**

The auditing skill is designed strictly for read-only discovery and inspection, adhering to the principle of least privilege.

Read-Only Safety: Excluding the Write tool prevents automated agents or audit scripts from accidentally modifying production configurations, deleting active infrastructure, or altering state files.

Separation of Concerns: The goal of an audit skill is to inspect state (Bash), read context (Read), and search logs/reports (Grep), leaving remediation actions as explicit human-approved decisions rather than automated side effects.

**2. What part is performed by Bash, and what part is performed by Claude?**

Performed by Bash: Executing the AWS CLI commands (the Gather phase), evaluating low-level conditional checks (the Evaluate phase), generating exit codes, and writing raw output to the report file.

Performed by Claude: Parsing raw audit logs, contextualizing security findings, translating technical output into clear risk analyses, estimating business/cost impacts, and providing safe remediation steps.

**3. Why is estimating cost/risk impact something the AI adds on top of a plain PASS/FAIL script?**

A plain Bash script only evaluates binary logic (e.g., if count > 0 then FAIL). It lacks semantic awareness of the broader business environment.

AI adds value on top of raw outputs by:

Contextualizing Severity: Explaining why a failure matters (e.g., distinguishing between an open SSH port exposed to the public internet vs. an internal VPC misconfiguration).

Quantifying Financial Impact: Predicting hidden AWS costs associated with misconfigurations (e.g., unattached EBS volumes incurring idle storage fees or unencrypted data risking regulatory compliance fines).

Prioritizing Remediation: Helping engineers prioritize fixes based on operational risk rather than treating all failed checks with equal urgency.
---

# Task 7 — Fix a Real Finding and Re-Verify

## Goal

Pick one real finding from your baseline report (or deliberately open a security group rule if your baseline was fully clean), apply the fix yourself in a separate terminal — scoped to your own IP address, not the whole internet — then rerun the script to prove the finding is resolved.

### Evidence

#### Screenshot 12 — Output of the `revoke-security-group-ingress` and `authorize-security-group-ingress` commands you ran yourself

![alt text](screenshots\sc12-T5-ass4-week6.png).

---

#### Screenshot 13 — Rerun of `./scripts/aws-audit.sh` showing the finding is now PASS

![alt text](screenshots\sc12-T5-ass4-week6.png).

---

### Notes You Must Write (Very Important)

**1. Which exact finding did you fix, and what command did you run?**

The finding fixed was the S3 Bucket Public ACL Exposure on the bucket pravin-portfolio-tayssir-eu-central-1.

**2. Why did you scope the new rule to your own IP address instead of leaving it open to `0.0.0.0/0`?**

Scoping ingress rules to a specific public IP address (e.g., 203.0.113.4/32) enforces the Principle of Least Privilege (PoLP) and reduces the attack surface:

Eliminates Unrestricted Access: 0.0.0.0/0 leaves ports (like SSH on 22 or MySQL on 3306) open to the entire internet, exposing your instances to relentless automated brute-force attacks and vulnerability scanners.

Restricts Entry to Trusted Sources: Restricting the CIDR block to /32 ensures that only connections originating strictly from your explicit management IP address are accepted by the AWS Security Group firewall.

**3. Did Claude execute the remediation command, or did you? Why does that matter?**

This separation matters because:

Human-in-the-Loop Safety: Destructive or state-changing actions (put-, authorize-, revoke-, delete-) require explicit human approval and execution to prevent automated tools from accidentally breaking live infrastructure or causing unintended service outages.

Strict Least-Privilege Scope: Giving AI agents execute/write permissions across cloud environments creates significant security risks. Restricting the agent to read-only tools ensures it acts solely as an advisor while keeping administrative authority with the engineer.

**4. Which phase of the Agentic Loop does the Bash script represent? Which phase does Claude's explanation represent? Which phase is you running the fix?**

The execution maps across the Agentic Loop (Gather $\rightarrow$ Analyze $\rightarrow$ Decide $\rightarrow$ Act) as follows:Bash Script: Represents the Gather & Evaluate (Analyze) phase. It issues read-only AWS CLI queries to fetch state data and runs programmatic if/else conditions to generate the baseline report.Claude's Explanation: Represents the Synthesize & Recommend (Decide) phase. It processes the raw execution log, assesses business risk/cost impacts, and Formulates targeted remediation recommendations.You Running the Fix: Represents the Act phase. You validate the proposed solution and execute the state-changing command in your environment to complete the loop.

---

# LinkedIn Post (Required)

## Goal

Create a LinkedIn post including:

- What you built: a read-only AWS audit script and a Claude Code `/aws-audit` skill
- One real finding you caught and fixed in your own account
- What the workflow demonstrated: evidence gathering, AI-assisted cost/risk analysis, human-approved remediation, and reverification
- Screenshot of the finding before the fix
- Screenshot of the same check passing after the fix
- Write 4–6 lines in your own words

Suggested tags:

`#DMIByPravinMishra #AWS #AgenticAI #ClaudeCode #DevOps`

### Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/taysir-ouaslati-9b6527a3_building-automated-aws-security-audits-why-activity-7498940409805561856-XLjz?utm_source=share&utm_medium=member_desktop&rcm=ACoAABX4AtoB0tpceeC8Jnqozhzdi2ViZ02bFHk

---

#### Screenshot of Published LinkedIn Post

![alt text](screenshots\linked-post-ass7-week6.png).

---

# Submission Instructions

Complete all tasks in sequence.

Your submission must include:

- All 13 required task screenshots
- Answers to every **Notes You Must Write** question
- `CLAUDE.md`
- `scripts/aws-audit.sh`
- `.claude/skills/aws-audit/SKILL.md`
- `reports/aws-audit-report.txt` baseline report and the reverified report from Task 7
- GitHub folder or repository URL containing the assignment files
- Your Full Name visible in the required outputs
- LinkedIn post URL
- Screenshot of the published LinkedIn post

Submit only a Google Doc link.

Add the GitHub URL inside the Google Doc.

Follow the Assignment Submission Guidelines.

---

# Completion Checklist

- [ ] Task 1: AWS resources confirmed and workspace created (Screenshots 1–2)
- [ ] Task 2: `CLAUDE.md` created with project context and safety rules (Screenshot 3)
- [ ] Task 3: Claude produced a read-only five-check audit plan before any script existed (Screenshot 4)
- [ ] Task 4: `aws-audit.sh` built, executable, and passes `bash -n` (Screenshots 5–7)
- [ ] Task 5: Baseline audit captured and saved with Full Name visible (Screenshots 8–9)
- [ ] Task 6: `/aws-audit` skill loads and runs successfully with no Write permission (Screenshots 10–11)
- [ ] Task 7: A real finding was fixed by you and reverified as PASS (Screenshots 12–13)
- [ ] Skill never executed a remediation command
- [ ] New security group rule is scoped to your own IP, not `0.0.0.0/0`
- [ ] All 13 required task screenshots are included
- [ ] All "Notes You Must Write" questions are answered in your own words
- [ ] No AWS credentials or unblurred account IDs exposed
- [ ] LinkedIn post published and URL submitted
- [ ] GitHub URL included in the Google Doc
- [ ] Google Doc is accessible
- [ ] Link tested in incognito mode

---

# Final Submission

Submit only your Google Doc link.

### Question

Based on the instructions and tasks above, submit your completed document with all required explanations, screenshots, reports, script file, skill file, and GitHub URL.

`Add your Google Doc link here`

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*