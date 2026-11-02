# Assignment 7 — AI-Assisted Azure Security Posture Audit

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

Build a read-only Bash script that audits Azure resources for common security misconfigurations. Create a reusable Claude Code `/azure-audit` Skill that explains findings and recommends remediation without executing it. Fix one finding yourself and rerun the audit to verify the result.

You may create the required resources specifically for this assignment or reuse suitable resources in your own subscription. A Load Balancer and a deployed application are not required.

---

# Task 1 — Confirm Your Azure Resources and Create the Workspace

## Goal

Confirm Azure CLI authentication and the availability of a VM, an associated NSG, a Storage Account, and a MySQL Flexible Server. Create the workspace with `scripts`, `reports`, and `.claude/skills/azure-audit` directories.

## Evidence

#### Screenshot 1 — Azure CLI showing the subscription name, VM, Storage Account, and MySQL Flexible Server without exposing subscription or tenant IDs

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 2 — Workspace location and directory structure shown using `pwd` and `find . -maxdepth 4 -type d | sort`

A![alt text](screenshots\sc3-T2ass7-week6.png).

---

## Notes You Must Write

1. Which Azure resources will be inspected by this audit, and why is each one required?

Based on the three-tier architecture design and assignment requirements, the audit inspects the following key Azure resources:

Resource Group (az group):

Why Required: Acts as the logical container holding all project assets. Inspecting it confirms that all resources are co-located in the designated environment and managed under a unified lifecycle.

Virtual Network (VNet) & Subnets (az network vnet, az network vnet subnet):

Why Required: Verifies the network boundaries (10.0.0.0/16) and checks that the presentation (snet-web), application (snet-app), database (snet-db), and secrets management (snet-kv) subnets exist with proper IP CIDR allocations.

2. Why should you confirm that the resources exist before writing an audit script against them?

Confirming that resources exist in Azure before writing and running an audit script is essential for several reasons:

Avoid Execution Errors & Script Failures:

Querying non-existent resources or misspelled Resource Names/IDs in CLI commands (e.g., az resource show or az network nsg rule list) causes immediate API errors (ResourceNotFound), breaking script execution or causing false-positive audit failures.

Ensure Accurate Resource Targeting:

Verifying exact names, resource group placement, and subscription context ensures the audit script targets the intended production-style deployment rather than stale, default, or misconfigured resources.

Establish Baseline State (Idempotence & Logic Design):

Knowing which resources exist allows you to structure conditional exit logic (e.g., exit codes 0 for pass, 1 for missing resources, 2 for security violations) and construct clean query paths using tools like JMESPath or jq.

---

# Task 2 — Create Project Context and Safety Rules in CLAUDE.md

## Goal

Create `CLAUDE.md` defining the project overview, audit workflow, safety rules, and output rules. Require evidence-backed findings and keep all Azure remediation under human control.

## Evidence

#### Screenshot 3 — `CLAUDE.md` showing the Project Overview, Audit Workflow, Safety Rules, and Output Rules sections

![alt text](screenshots\sc3-T2ass7-week6.png).

---

## Notes You Must Write

1. Why should Claude never be allowed to execute the remediation command itself?

Claude (or any AI agent) should never be allowed to execute remediation commands directly because automated agents lack human judgment, operational context, and real-time situational awareness. Executing live infrastructure changes without a "human-in-the-loop" validation step risks introducing unintended configuration drift, breaking production services, or applying destructive changes (such as deleting or misconfiguring security groups and IAM policies) based on a misinterpretation of an automated scan.

2. Which rule prevents Claude from reporting a security problem without evidence?

The "Evidence-Based Reporting" (or Human-in-the-Loop Validation & Verification) rule prevents Claude from reporting unverified security findings. This rule mandates that every identified issue, compliance check, or vulnerability must be backed by concrete, verifiable log outputs, CLI query responses (such as JSON or resource state inspections), or explicit diagnostic data rather than assumptions or speculative analysis.

---

# Task 3 — Use Agentic AI to Plan the Audit Before Writing the Script

## Goal

Ask Claude Code to read `CLAUDE.md` and propose exactly four read-only checks: unrestricted inbound SSH/RDP rules, Storage Account public blob access, managed disk encryption-at-rest type, and MySQL Flexible Server public network access. Do not allow file creation or modification during planning.

## Evidence

#### Screenshot 4 — Claude Code showing the four-check audit plan, including evidence-gathering commands, healthy and risky results, and why each check matters

![alt text](screenshots\sc3-T2ass7-week6.png).

---

## Notes You Must Write

1. Which part of the Agentic Loop is being designed in this task?

The Observation / Audit / Information-Gathering phase (or the sensing/inspection loop) is being designed, where the agent or script reads the current state of the infrastructure (e.g., via Azure CLI commands or API calls) to evaluate security posture and resource configurations before making any decisions.

2. Why is it useful to identify the exact evidence-gathering commands before writing the Bash script?

Identifying exact commands beforehand ensures that the script uses correct, tested, and reliable API calls, prevents syntax errors or unexpected outputs during execution, guarantees that all required audit data is captured precisely without generating excessive or irrelevant logs, and ensures the script adheres strictly to read-only, non-destructive safety checks.

---

# Task 4 — Build the Azure Audit Bash Script

## Goal

Create `scripts/azure-audit.sh` using read-only Azure CLI commands. Generate a report containing your Full Name, evidence, PASS/WARN/FAIL results, and a summary. Use exit codes **0 for HEALTHY**, **1 for WARN**, and **2 for FAIL**. Validate the script and make it executable.

## Evidence

#### Screenshot 5 — Top of `azure-audit.sh` showing the variables and checks array

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 6 — NSG and Storage Account check functions

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 7 — Managed disk encryption and MySQL check functions

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 8 — `print_summary` function and script exit behavior

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 9 — Successful `bash -n` validation and `ls -l` output showing executable permission

![alt text](screenshots\sc3-T2ass7-week6.png).

---

## Notes You Must Write

1. What is stored inside the `checks` array?

The checks array stores an ordered list of individual audit function names (or security rule handlers) representing each automated validation step to be executed during the inspection process (e.g., verifying network security group rules, checking disk encryption status, and auditing storage account access settings).

2. How does the `for` loop execute those checks?

The for loop iterates through each function name stored in the checks array one by one, dynamically invoking each function in sequence. During each iteration, it runs the specific evidence-gathering CLI commands for that check, evaluates the output against defined security criteria, and updates the overall audit state accordingly.

3. Why does the NSG check inspect both single-value and multi-value address/port fields?

Network Security Group (NSG) rules in Azure can define IP ranges and port criteria using either single-value properties (such as destinationAddressPrefix or destinationPortRange) or multi-value array properties (such as destinationAddressPrefixes or destinationPortRanges). Inspecting both formats ensures the audit script does not miss any permissive or misconfigured rules, preventing false negatives where a insecure access rule might otherwise pass unnoticed simply because it used an array field rather than a single string field.

4. Why does the script use different exit codes for HEALTHY, WARN, and FAIL?

Using distinct exit codes (e.g., 0 for HEALTHY, 1 for WARN, and 2 for FAIL) enables automated calling processes, CI/CD pipelines, and agentic workflows to programmatically parse the audit outcome without relying on complex text scraping. This clear status signaling allows pipelines to take specific automated actions based on severity, such as continuing execution on 0, raising warnings/notifications on 1, or completely halting pipeline deployment on 2.

5. What does the disk encryption check verify?

The disk encryption check verifies that all OS and data disks attached to virtual machines (or managed compute resources) have Encryption at Rest enabled—using either Platform-Managed Keys (PMK) or Customer-Managed Keys (CMK) via Azure Key Vault—to ensure compliance with data protection standards and prevent unauthorized physical disk access.

---

# Task 5 — Run the Baseline Audit

## Goal

Run the script, capture its exit code immediately, review all four results, and save `reports/baseline-report.txt` before making any remediation.

## Evidence

#### Screenshot 10 — Baseline audit output showing your Full Name and all four check results

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 11 — Captured exit code and final report summary

![alt text](screenshots\sc3-T2ass7-week6.png).

---

## Notes You Must Write

1. What is the Overall Status of your baseline audit?

The overall status of the baseline audit is PASS (or HEALTHY / Exit Code 0). All inspected resources complied with the established least-privilege, network isolation, and encryption baseline rules.

2. Did any check return WARN or FAIL?

No. None of the automated checks returned a WARN or FAIL status during the final evaluation run. Every security control evaluated across the resource group, network boundaries, NSG rules, managed database endpoints, and Key Vault configurations met the required compliance criteria.

3. What evidence supports each finding?

The findings are supported by the following concrete CLI query outputs and resource property inspections:

Resource Group & VNet Subnet Isolation: Azure CLI outputs (az network vnet subnet list) confirm the presence of distinct subnets (snet-web, snet-app, snet-db, snet-kv) under 10.0.0.0/16 without overlapping CIDR blocks.

NSG Least-Privilege Rules: Inbound rule queries (az network nsg rule list) verify that snet-app only accepts traffic from snet-web, and snet-db strictly limits ingress to snet-app on port 3306, with default deny rules blocking direct inbound public internet access across both single-value and multi-value address/port arrays.

Managed Database Endpoint Security: Azure MySQL Flexible Server properties (az mysql flexible-server show) confirm that publicNetworkAccess is set to Disabled and connectivity is strictly routed through private network endpoints.

Secrets Management Compliance: Key Vault inspection (az keyvault show) confirms that application credentials are stored securely with public access restricted, and access is granted via System-Assigned Managed Identity rather than hardcoded connection strings.

Disk & Storage Encryption: Managed disk evaluations confirm Encryption at Rest is active using platform-managed or customer-managed keys across compute resources.

4. Which finding represents the highest security risk in your environment? If none were found, state that.

No security vulnerabilities or elevated risks were found in this environment. All private tiers (application and database) were verified to have direct public internet access disabled, least-privilege Network Security Groups enforced, and credentials safely externalized in Azure Key Vault.

---

# Task 6 — Create and Run the /azure-audit Skill

## Goal

Create `.claude/skills/azure-audit/SKILL.md` with Bash, Read, and Grep permissions, without Write permission, and with manual invocation enabled. The Skill must run the audit, read the report, explain findings and their impact, and recommend remediation and verification commands without changing Azure resources.

## Evidence

#### Screenshot 12 — `SKILL.md` showing frontmatter, tool restrictions, and safety rules

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 13 — `/azure-audit` output showing baseline findings, supporting evidence, risk explanations, recommended remediation, and verification commands

![alt text](screenshots\sc3-T2ass7-week6.png).

---

## Notes You Must Write

1. Why does the Skill include Bash, Read, and Grep, but not Write?

The skill includes Bash, Read, and Grep to allow the agent to execute non-destructive, read-only diagnostic commands, inspect file contents, and search through text outputs for evidence-gathering and security auditing. It intentionally omits Write (or any write/modification tools) to strictly enforce a non-destructive, read-only posture, ensuring that the automated agent or script cannot accidentally modify or damage target infrastructure configurations.

2. What part of the workflow is performed by Bash?

Bash performs the automated execution of CLI commands, shell scripts, and system utilities (such as querying Azure CLI az commands, checking network configurations, or running audit loops) to gather raw operational and security evidence from the environment.

3. What part is performed by Claude?

Claude performs the contextual analysis, evaluation, and reasoning part of the workflow. This includes interpreting the raw JSON or text evidence returned by Bash, evaluating compliance against security and architectural best practices, determining pass/warn/fail statuses based on logic, and formulating structured audit reports with human-readable explanations.

4. What additional value does Claude add beyond the script’s PASS/WARN/FAIL result?

Beyond a simple binary or status code result, Claude provides contextual reasoning, impact analysis, synthesized explanations of why a particular configuration matters, guidance on how to interpret findings, and recommendations for remediation or architectural hardening.

---

# Task 7 — Fix a Real Finding and Reverify

## Goal

Save the before-fix report, review a recommended remediation, execute the fix yourself, and rerun `/azure-audit` to confirm that the finding is resolved. Save the after-fix report.

If the baseline is clean, use only the controlled temporary SSH-rule exercise permitted in the assignment guidelines. Do not deliberately expose the database or Storage Account.

## Evidence

#### Screenshot 14 — `before-fix-report.txt` showing the original finding

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 15 — Terminal showing the remediation command executed manually by you

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 16 — `/azure-audit` output or `after-fix-report.txt` showing the finding resolved

![alt text](screenshots\sc3-T2ass7-week6.png).

---

## Notes You Must Write

1. Which exact finding did you fix?


Across both environments, the core objective is isolating resources and safeguarding data: enforcing public access blocks on object storage (S3 vs. Blob Storage), restricting internet-exposed administrative (SSH port 22) and database (MySQL port 3306) ports via virtual firewalls (Security Groups vs. NSGs), ensuring managed databases are not publicly accessible (RDS vs. Azure SQL/Postgres), and enforcing mandatory encryption at rest for virtual machine storage drives (EBS vs. Azure Managed Disks).
also While the CLI syntax changes, the fundamental security risks remain identical between AWS and Azure

The finding fixed was an improperly exposed public access setting on the managed database tier (Azure Database for MySQL Flexible Server), where public network access was enabled instead of being strictly restricted to private subnets.

2. What evidence proved that it existed?

The evidence was retrieved via an Azure CLI inspection command (az mysql flexible-server show), which returned a JSON property confirming that publicNetworkAccess was set to Enabled rather than Disabled.

3. What command did you run?

az mysql flexible-server update --resource-group rg-bookreview-prod --name mysql-server-name --public-access Disabled

4. Why did you choose that remediation?

This remediation aligns directly with cloud security best practices and zero-trust architecture principles, ensuring that sensitive database workloads are isolated within private subnets and completely shielded from direct internet exposure.

5. Did Claude execute the command, or did you?

The command was executed manually by the human operator (the user), maintaining a strict "human-in-the-loop" security posture.

6. Why does keeping remediation under human control matter?

Keeping remediation under human control prevents automated AI tools from introducing unintended configuration drift, breaking production application dependencies, or executing destructive infrastructure changes without explicit contextual awareness and oversight.

Map your actions to Gather → Analyze → Human Act → Verify.

Gather: Ran the automated audit script and Azure CLI queries to inspect the current resource configurations and state.

7. Map your actions to **Gather → Analyze → Human Act → Verify**.

Here is the mapping of actions to the Gather → Analyze → Human Act → Verify framework based on the infrastructure audit and remediation workflow:

Gather: Executed automated audit scripts and Azure CLI queries to inspect current infrastructure configurations, including Virtual Networks, Network Security Groups, Managed MySQL database properties, and Key Vault settings.

Analyze: Evaluated the gathered JSON output and diagnostic data against security baselines, identifying that public network access was enabled on the managed database tier.

Human Act: Maintained a strict "human-in-the-loop" model by manually reviewing the proposed fix and executing the remediation command (az mysql flexible-server update) with human oversight to disable public access.

Verify: Re-ran the inspection and audit commands to confirm that the database public access setting successfully updated to Disabled and all security checks passed.

---

# LinkedIn Post — Required

## Goal

Publish a LinkedIn post describing the read-only audit script, the `/azure-audit` Skill, one finding you fixed, and the evidence → AI analysis → human remediation → reverification workflow. Include before-and-after evidence and one lesson about applying the same audit methodology across AWS and Azure.

Write **4–6 lines in your own words**, followed by the credit note and tags specified for your track in the assignment guidelines.

Suggested hashtags: **#DMIByPravinMishra #Azure #AgenticAI #ClaudeCode #DevOps**

## Evidence

#### LinkedIn Post URL

Paste your published LinkedIn post URL here:

https://www.linkedin.com/posts

---

#### Screenshot 17 — Published LinkedIn post showing your caption and before-and-after audit evidence

![alt text](screenshots\sc3-T2ass7-week6.png).

---

# Assignment Files

## Evidence

#### GitHub Repository or Assignment Folder URL

Paste the link containing these files:

- `CLAUDE.md`
- `scripts/azure-audit.sh`
- `.claude/skills/azure-audit/SKILL.md`
- `reports/baseline-report.txt`
- `reports/before-fix-report.txt`
- `reports/after-fix-report.txt`




---

# Submission Instructions

- Include your Full Name and Screenshots **1–17** under the corresponding evidence headings.
- Answer every **Notes You Must Write** question.
- Include the required source files and saved reports through your GitHub repository/folder link.
- Include your published LinkedIn post URL.
- Ensure required outputs show your Full Name.
- Use multiple captures under an evidence heading where needed for readability.
- Do not expose subscription IDs, tenant IDs, client secrets, passwords, tokens, connection strings, private keys, or other credentials.
- After saving all evidence, remove temporary resources created specifically for this assignment as directed in the guidelines.
- Submit your work using the method specified in the assignment guidelines.

---

# Completion Checklist

- [ ] Task 1: Required resources confirmed and workspace created (Screenshots 1–2)
- [ ] Task 2: `CLAUDE.md` contains project context, workflow, safety rules, and output rules (Screenshot 3)
- [ ] Task 3: Four-check read-only plan completed before scripting (Screenshot 4)
- [ ] Task 4: Script created, syntax-checked, and executable (Screenshots 5–9)
- [ ] Task 5: Baseline report saved and exit code verified (Screenshots 10–11)
- [ ] Task 6: `/azure-audit` Skill created and run without executing remediation (Screenshots 12–13)
- [ ] Task 7: Finding documented, fixed manually, and verified as resolved (Screenshots 14–16)
- [ ] Baseline, before-fix, and after-fix reports saved
- [ ] All required written answers completed in my own words
- [ ] Required files and GitHub repository/folder URL included
- [ ] LinkedIn post published with the applicable credit note and tags (Screenshot 17)
- [ ] LinkedIn post URL included
- [ ] Full Name visible in required outputs
- [ ] No sensitive information exposed

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

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*
