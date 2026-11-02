# Assignment 5 — AI-Assisted Azure DevOps Dual-Pipeline Failure Triage

Part of the DevOps Micro Internship (DMI) — Agentic AI Track

---

## Student Information

**Full Name:** Tayssir Oueslati

**GitHub Repository or Fork URL:** https://github.com/siraa/devops-micro-internship-pravinmishra

**Public LinkedIn Post URL:** https://lnkd.in/p/dTYXxVDR

---

## Purpose

In this assignment, I configured an AI-assisted, read-only failure-triage workflow for the EpicBook Infrastructure and Application Pipelines. The workflow uses Bash to gather Azure DevOps pipeline evidence and Claude Code to analyze the evidence and recommend a recovery action while keeping all changes under human control.

---

# Task 0 — Verify Tools, Authentication, and Pipeline Details

## Goal

Verify the required tools, Azure DevOps authentication, organization and project details, and numeric pipeline IDs.

No screenshot is required for this task.

---

# Task 1 — Capture the Healthy Baseline and Prepare the Supplied Files

## Goal

Confirm that both EpicBook pipelines are healthy and place the supplied assignment files in the correct repository locations.

## Evidence

### Screenshot 1 — Healthy Baseline for Both Pipelines

Terminal output showing the latest completed Infrastructure and Application Pipeline runs with successful results.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

## Notes

### 1. What proves that both pipelines were healthy before the drill?

The latest completed Infrastructure and Application Pipeline runs both showed a successful status. Their execution logs confirmed that the required stages and jobs completed without errors. The initial triage report also classified both pipelines as HEALTHY.

### 2. Why is a healthy baseline necessary before introducing a controlled failure?

A healthy baseline provides a reliable reference point for comparison. It helps distinguish problems introduced during the incident drill from pre-existing issues and makes it easier to verify that the recovery process was successful.

---

# Task 2 — Configure and Review the Supplied CLAUDE.md

## Goal

Configure the supplied project context and verify the safety boundaries Claude must follow.

## Evidence

### Screenshot 2 — CLAUDE.md Context and Safety Rules

`CLAUDE.md` open in the editor with the Project Overview, Incident Workflow, Safety Rules, and Output Rules visible.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

## Notes

### 1. Why does Claude need project-specific operational context?

Claude needs project-specific context to understand the infrastructure, pipeline architecture, deployment process, and operational requirements. This helps it analyze failures accurately and provide recommendations that are relevant to the project.

### 2. Which rules keep the human responsible for the recovery action?

[Write your answer here.]The rules require Claude to investigate, classify, and recommend a solution without automatically applying changes. A human must review the evidence, approve the proposed fix, execute it manually, and verify the recovery.

### 3. Which rules protect pipeline credentials and application secrets?

The rules prohibit exposing PATs, SSH private keys, passwords, cloud credentials, and application secrets in reports or screenshots. Sensitive values must be stored securely and sanitized before sharing diagnostic evidence.

---

# Task 3 — Configure and Validate the Supplied Pipeline Triage Script

## Goal

Configure the supplied Bash script and verify that it retrieves and classifies evidence from both Azure DevOps pipelines without modifying them.

## Evidence

### Screenshot 3 — Pipeline Triage Script Configuration

Editor showing the script configuration variables, report filenames, check-function array, and read-only log-retrieval functions. Ensure that no token is visible.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

---

### Screenshot 4 — Script Validation

Terminal showing successful Bash syntax validation and executable file permission.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

## Notes

### 1. Why are pipeline metadata and step console logs handled separately?

Pipeline metadata provides information such as the pipeline name, run ID, status, branch, and execution details. Step console logs contain the actual output and error messages from individual pipeline tasks. Both are needed to identify the failure and understand its cause.

### 2. How does the script obtain the actual console logs?

The script uses the Azure DevOps REST API to retrieve the logs associated with a pipeline run. It identifies the relevant failed step and downloads its console output for further analysis.

### 3. How does the check-function array control the classification loop?

The check-function array contains the diagnostic functions that the script executes. The classification loop processes these functions one by one, checking the collected evidence against each failure category and identifying the appropriate classification.

### 4. What prevents a failed but unmatched run from being reported as healthy?

The script checks the actual pipeline run status and the results of its classification functions. If a run has failed but no known failure category matches the evidence, it must be reported as an unknown or unclassified failure rather than HEALTHY.

### 5. Why are different exit codes useful to another automation tool?

Different exit codes allow another automation tool to identify the result without interpreting the entire report. For example, exit code 0 can indicate a healthy state, while non-zero codes can indicate a detected incident, an unknown failure, or an execution error, depending on the script's implementation.

---

# Task 4 — Run and Understand the Healthy-State Report

## Goal

Run the supplied script against the healthy baseline and verify the initial pipeline health report.

## Evidence

### Screenshot 5 — Healthy Pipeline Report

Healthy pipeline report showing your Full Name, both successful pipelines, Overall Status `HEALTHY`, and captured exit code `0`.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

## Notes

### 1. What evidence proves that both pipelines are healthy?

The report shows that the latest Infrastructure and Application Pipeline runs completed successfully. Their statuses are successful, the overall classification is HEALTHY, and the script returns exit code 0.

### 2. Why must the baseline exit code be verified before the incident drill?

Verifying the baseline exit code confirms that the triage script completed successfully and that the initial environment was healthy. This ensures that any later failure can be compared against a known working state.

---

# Task 5 — Configure and Test the Supplied /pipeline-triage Skill

## Goal

Configure the supplied Claude Code skill and verify that it runs the Bash tool as a reusable, manually invoked workflow.

## Evidence

### Screenshot 6 — Pipeline-Triage Skill Definition

`SKILL.md` showing the frontmatter, manual-invocation setting, narrowly scoped tools, safety rules, and required output structure.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

---

### Screenshot 7 — Healthy Skill Result

Healthy `/pipeline-triage` result showing that both pipelines are healthy and no fix is required.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

## Notes

### 1. Why is `disable-model-invocation: true` appropriate for this skill?

It ensures that the skill is invoked manually by the user rather than automatically by Claude. This gives the human control over when the diagnostic workflow runs.

### 2. Why should the skill avoid broad Bash approval?

Broad Bash approval could allow commands that modify files, change infrastructure, expose credentials, or trigger deployments. Restricting Bash permissions reduces security risks and keeps the diagnostic process read-only.

### 3. What work is performed by Bash, and what work is performed by Claude?

Bash executes the diagnostic script, retrieves pipeline information and logs, classifies evidence, and generates the reports. Claude interprets the results, explains the likely cause, and provides a recommended recovery action for human review.

### 4. Why are permission rules required in addition to written safety instructions?

Written instructions describe the expected behavior, but permission rules technically restrict which actions can be performed. Both are necessary to reduce the risk of unauthorized modifications or accidental execution of dangerous commands.

---

# Task 6 — Introduce a Safe Failure in the Application Pipeline

## Goal

Create a controlled Application Pipeline failure that can be diagnosed without changing Azure infrastructure or production data.

## Evidence

### Screenshot 8 — Controlled Application Pipeline Failure

Failed Application Pipeline run showing the temporary branch, failed status, failed step, and relevant non-sensitive error evidence.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

## Notes

### 1. What exact failure did you introduce?

I introduced a controlled failure by temporarily modifying the Application Pipeline configuration on a separate branch. The change caused the pipeline to fail during the [INSERT FAILED STEP OR TASK NAME] step.

### 2. Which category should detect it?

The triage script should classify the failure under the category that matches the actual error evidence, such as a pipeline configuration error, deployment error, or command execution failure.

### 3. Why is the failure safe and easily reversible?

The failure was introduced on a temporary branch rather than directly on the main branch. It did not require changes to Azure infrastructure or production data, and it could be reversed by restoring the original pipeline configuration.

### 4. How did you prevent the deliberate failure from reaching `main` or changing the deployed application?

I isolated the change on a temporary branch and did not merge it into main. I also avoided applying infrastructure changes or deploying the deliberately broken configuration to the live application.

---

# Task 7 — Diagnose and Save the Incident Evidence

## Goal

Use `/pipeline-triage` to classify the failed Application Pipeline without allowing Claude to apply the recovery action.

## Evidence

### Screenshot 9 — Failed-State Diagnosis and Incident Report

`/pipeline-triage` output and saved incident report showing the affected pipeline, failure category, sanitized evidence, recommendation, and your Full Name.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

## Notes

### 1. Which failure category was identified?

The triage script identified the failure as [INSERT THE ACTUAL FAILURE CATEGORY FROM YOUR INCIDENT REPORT].

### 2. What exact evidence supported the diagnosis?

The diagnosis was supported by the failed Application Pipeline status, the affected pipeline step, and the relevant error messages retrieved from the pipeline console logs.

### 3. Did Claude apply the fix or rerun the pipeline? Why is that important?

No. Claude only analyzed the available evidence and recommended a recovery action. The fix and pipeline rerun were left to human approval and execution. This preserves human oversight and prevents unauthorized changes to the deployment environment.

### 4. Which part represents Gather, and which part represents Analyze?

Gather represents collecting pipeline metadata, execution status, console logs, and other diagnostic evidence. Analyze represents interpreting the collected information, identifying the failure category, explaining the likely cause, and recommending a solution.

---

# Task 8 — Apply the Human-Reviewed Fix and Verify Recovery

## Goal

Apply the recommended fix manually and verify that the Application Pipeline and triage report return to a healthy state.

## Evidence

### Screenshot 10 — Corrected Application Pipeline Run

Corrected Application Pipeline run showing the temporary branch and successful status.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

---

### Screenshot 11 — Recovery Triage Result

Recovery `/pipeline-triage` output showing Overall Status `HEALTHY`, exit code `0`, your Full Name, and both saved report filenames.

![alt text](screenshots\Capture d'écran 2026-11-02 181632.png)

## Notes

### 1. What exact fix did you apply?

I manually applied the recommended fix by [DESCRIBE THE EXACT CHANGE YOU MADE TO THE PIPELINE OR CONFIGURATION]. I then committed the correction to the temporary branch and reran the Application Pipeline.

### 2. Did the fix match Claude’s recommendation? Explain briefly.

Yes. The manual correction followed Claude’s recommendation and addressed the issue identified in the incident report. I reviewed the proposed change before applying it.

### 3. What evidence proves that the pipeline recovered?

The corrected Application Pipeline run completed successfully. The recovery triage report showed an overall status of HEALTHY, exit code 0, and successful results for both the Infrastructure and Application Pipelines.

### 4. Why is a second triage run required after the pipeline becomes green?

A successful pipeline run confirms that the deployment process completed, but a second triage run independently checks the latest pipeline results. It confirms that the incident is resolved and that the overall environment has returned to a healthy state.

### 5. What risk would be created if Claude could automatically edit, push, approve, and rerun the pipeline?

Automatic access to these actions could allow an incorrect fix to be committed, unauthorized changes to reach the main branch, or an unsafe deployment to be triggered. Keeping a human approval step ensures that recovery actions are reviewed and controlled.

---

# LinkedIn Post — Mandatory

## LinkedIn Post URL

https://lnkd.in/p/dTYXxVDR

## Evidence

### Screenshot 12 — Published LinkedIn Post

Published LinkedIn post showing its text and at least one image or link.

![alt text](week-10-azure-devops\screenshots\post-ass1-week10.png)

---

# Required Repository Files

Confirm that the following files are available in your repository:

* [ ] `CLAUDE.md`
* [ ] `pipeline-triage.sh`
* [ ] `.claude/skills/pipeline-triage/SKILL.md`
* [ ] `reports/incident-failure-report.txt`
* [ ] `reports/recovery-report.txt`

---

# Submission Instructions

* Complete all tasks in sequence.
* Include all 12 required screenshots.
* Answer every Notes question in your own words.
* Include your GitHub repository or fork URL.
* Include your public LinkedIn post URL.
* Ensure your Full Name appears in the required reports.
* Do not include raw logs containing sensitive information.
* Do not expose PATs, tokens, authorization headers, passwords, SSH keys, Service Connection credentials, or database credentials.

---

# Completion Checklist

* [ ] Both Azure DevOps pipelines were healthy before the drill.
* [ ] The supplied files were copied to the correct repository locations.
* [ ] Only the required student-specific placeholders were updated.
* [ ] `CLAUDE.md` contains the required context and safety rules.
* [ ] `pipeline-triage.sh` passed Bash syntax validation.
* [ ] The script has executable permission.
* [ ] The script uses read-only Azure DevOps operations.
* [ ] The script retrieves pipeline metadata and console logs.
* [ ] No token or password is stored in the script.
* [ ] The healthy baseline reported `HEALTHY` with exit code `0`.
* [ ] `/pipeline-triage` was invoked manually.
* [ ] The skill does not have broad Bash approval.
* [ ] The controlled failure affected only the Application Pipeline.
* [ ] The failure occurred before deployment changes were applied.
* [ ] The deliberate failure was not merged into `main`.
* [ ] The failed-state report was saved before applying the fix.
* [ ] Claude diagnosed the failure but did not apply the fix.
* [ ] The fix was reviewed and applied manually.
* [ ] The corrected Application Pipeline completed successfully.
* [ ] The recovery triage reported `HEALTHY` with exit code `0`.
* [ ] `incident-failure-report.txt` exists.
* [ ] `recovery-report.txt` exists.
* [ ] All Notes questions have been answered.
* [ ] All 12 screenshots have been added.
* [ ] The GitHub repository or fork URL has been included.
* [ ] The LinkedIn post is public.
* [ ] The LinkedIn post URL has been included.
* [ ] No sensitive information is exposed.

---

# Final Submission

**Full Name:** [Enter your full name]

**GitHub Repository or Fork URL:** [Paste your repository URL]

**LinkedIn Post URL:** [Paste your public LinkedIn post URL]

---

*This submission is part of the DevOps Micro Internship (DMI) — Agentic AI Track.*
