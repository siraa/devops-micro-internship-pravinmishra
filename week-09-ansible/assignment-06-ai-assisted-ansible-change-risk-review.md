# Assignment 6 — AI-Assisted Ansible Change Risk Review

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will build an AI-assisted Ansible risk-review workflow using `ansible-playbook --check --diff`, Bash scripting, and Claude Code.

You will review possible server changes before applying them, classify risky tasks, and keep the final apply decision under human control.

---

# Task 1 — Confirm EpicBook Connectivity and Create the Workspace

## Goal

Confirm that your previous EpicBook Ansible project is working before creating the risk-review automation.

### Evidence

#### Screenshot 1 — Output of `ansible web -i inventory.ini -m ping`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 2 — Output of `ansible-playbook -i inventory.ini site.yml --syntax-check`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 3 — Output of `pwd` and `find . -maxdepth 4 -type d | sort`

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What proves that Ansible can reach your EpicBook VM?**

The successful execution of the Ansible ping command proves that Ansible can reach the EpicBook VM:

ansible web -i inventory.ini -m ping

The response "ping": "pong" confirms that the SSH connection works and Ansible can communicate with the EC2 instance.

---

**2. Why should you confirm playbook syntax before building a risk-review script?**

Checking the playbook syntax helps identify YAML formatting errors, incorrect indentation, and Ansible configuration problems before execution. This ensures that the playbook is valid and reduces the risk of failures or unintended changes when running the deployment or risk-review script.

---

# Task 2 — Create Project Context and Safety Rules in CLAUDE.md

## Goal

Create a `CLAUDE.md` file that tells Claude Code how this project must behave.

### Evidence

#### Screenshot 4 — `CLAUDE.md` open in VS Code or terminal showing the safety rules

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. Why should Claude Code have project-specific safety rules?**

Project-specific safety rules help Claude Code understand the project's structure, configuration, and security requirements. They prevent unsafe actions, protect important files and credentials, and ensure that suggested changes follow the project's rules.

---

**2. Why should the human run the real Ansible playbook manually?**

The human should run the real Ansible playbook manually to maintain control over the deployment. This allows them to review the changes, confirm that everything is correct, and avoid unexpected modifications to the AWS infrastructure or application

---

**3. Which rule prevents Claude Code from applying changes automatically?**

The rule requiring human approval before executing deployment commands prevents Claude Code from applying changes automatically. Claude Code can review files and suggest commands, but the human must approve and run the real Ansible playbook.

---

# Task 3 — Ask Claude Code to Plan the Risk Review

## Goal

Use Claude Code to produce a read-only plan before writing the Bash script.

### Evidence

#### Screenshot 5 — Claude Code showing the four-category risk-classification plan

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. Which part of this task represents the Gather phase?**

The Gather phase is when Claude Code reads and collects information about the project, including the Ansible inventory, playbooks, roles, configuration files, and project-specific safety rules.

---

**2. Which part represents the Analyze phase?**

The Analyze phase is when Claude Code reviews the collected information, checks the Ansible configuration and playbook syntax, identifies potential risks, and prepares recommendations without applying changes.

---

**3. How did you verify Claude Code did not create or edit files?**

I checked the Git status and reviewed the project files before and after the analysis. There were no new or modified files, confirming that Claude Code only inspected the project and did not make changes.

---

# Task 4 — Build the Ansible Risk Review Script

## Goal

Create a Bash script that runs an Ansible dry run and classifies risky changes.

### Evidence

#### Screenshot 6 — Top section of `ansible-check-review.sh` showing `full_name`, `playbook_path`, `inventory_path`, and the `checks` array

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 7 — Middle section showing `extract_changed_tasks` and `check_tasks_matching_pattern`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 8 — Bottom section showing the loop, summary, and exit behavior

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 9 — Output of `bash -n ansible-check-review.sh` and `ls -l ansible-check-review.sh`

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What is stored in the `changed_tasks` array?**

The changed_tasks array stores the Ansible tasks that would make changes to the server. It helps identify which tasks are expected to modify the system.

---

**2. Which function finds changed tasks from the Ansible output?**

The find_changed_tasks() function searches the Ansible output and identifies the tasks marked as changed.

---

**3. Why does the script use `--check --diff`?**

The --check option runs Ansible in check mode to simulate changes without applying them. The --diff option shows the differences that would be made. Together, they help review potential changes safely before deployment.

---

**4. Why does the script use different exit codes for healthy, warning, and failed results?**

Different exit codes make it easier to identify the result of the risk review. A healthy result means no important issues were found, a warning indicates potential changes that need attention, and a failed result indicates an error that must be investigated. Other tools and automation pipelines can also use these codes to react to the result.

---

# Task 5 — Run the Baseline Dry-Run Review

## Goal

Run the script against your current EpicBook playbook and confirm the baseline risk status.

### Evidence

#### Screenshot 10 — Output of `./ansible-check-review.sh`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 11 — Output of `echo "Captured Exit Code: $script_exit_code"` and `cat reports/ansible-risk-report.txt`

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What was the overall status of your baseline run?**

The overall status of my baseline run was healthy. The script completed the risk review successfully without reporting any critical errors.

---

**2. Did any tasks report `changed`?**

No tasks reported changed. The baseline run did not identify any tasks that would modify the server.

---

**3. Were any changed tasks flagged as risky?**

No changed tasks were flagged as risky because the baseline run did not report any changed tasks.

---

**4. What does the script exit code mean?**

The exit code indicates the result of the risk review. It helps identify whether the run was healthy, produced warnings, or failed. A zero exit code generally indicates success, while non-zero codes indicate warnings or errors, depending on the script's implementation.

---

# Task 6 — Create and Run the Claude Code Skill

## Goal

Turn the Bash script into a reusable Claude Code skill called `/ansible-risk-review`.

### Evidence

#### Screenshot 12 — `SKILL.md` showing the frontmatter, allowed tools, and safety rules

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 13 — Claude Code output after running `/ansible-risk-review`

Add your screenshot here.![alt text](screenshots\output-ass5-week9.png)
---

### Notes

Answer the following in your own words:

**1. Why does this skill allow `Bash`, `Read`, and `Grep`?**

These tools allow Claude Code to execute read-only commands, inspect project files, and search for relevant information. They help Claude Code analyze the project without modifying it.

---

**2. Why does this skill not allow file editing?**

File editing is not allowed to prevent accidental or unauthorized changes. The purpose of the skill is to analyze risks, not to modify the project or apply configurations.

---

**3. What part is handled by Bash?**

Bash runs the Ansible commands, performs the syntax checks and check-mode execution, and collects the command output for the risk review.

---

**4. What part is handled by Claude Code?**

Claude Code reads and analyzes the Ansible output, identifies potential risks, interprets the results, and explains which tasks may change the server.

---

**5. Why is this better than asking Claude Code if the playbook is safe without giving it evidence?**

This approach is more reliable because Claude Code analyzes real Ansible output instead of guessing. It provides evidence-based results and helps identify potential risks more accurately before applying changes.

---

# Task 7 — Introduce a Controlled Risky Change and Let the Skill Catch It

## Goal

Add a small controlled risky change in your lab playbook and confirm the script and Claude Code catch it before applying.

### Evidence

#### Screenshot 14 — The added risky task inside the role file

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 15 — Output of `./ansible-check-review.sh`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 16 — Claude Code `/ansible-risk-review` output showing the risky finding

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 17 — Output of `cat reports/risky-change-report.txt`

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. Which risk category did the added task fall into?**

The added task fell into the risky category because it could make a change to the server configuration.

---

**2. What evidence proves the task would change something?**

The Ansible check-mode report showed that the task was marked as changed. This indicated that Ansible expected the task to modify the system.

---

**3. Did Claude Code apply the playbook?**

No. Claude Code only analyzed the Ansible output and reported the potential risk. It did not apply the playbook

---

**4. Why is it important that Claude Code only analyzed the risk?**

It is important because it keeps control in human hands and prevents unauthorized or unexpected changes to the server. The human can review the risks and decide whether to approve the changes.

---

**5. Which phase of the Agentic Loop is represented by the Bash report?**

The Bash report represents the Analyze phase because it processes the Ansible output, identifies potential changes, and reports the detected risks.

---

# Task 8 — Apply as the Human, Verify, and Write the Change Summary

## Goal

Review the risky-change report, apply the playbook manually as the human operator, and verify the result.

### Evidence

#### Screenshot 18 — Output of the real playbook run showing the final recap with `failed=0`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 19 — Output of `ansible web -i inventory.ini -m ping`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 20 — Second `/ansible-risk-review` output after applying the change

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 21 — Output of `ls -lah reports`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 22 — `change-summary.md` showing all required sections and your Full Name

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What command did you run to apply the change for real?**

I ran the Ansible playbook manually using:

ansible-playbook -i inventory.ini site.yml --ask-vault-pass

This command applied the configuration changes to the EpicBook VM.

---

**2. Who made the final decision to apply the playbook?**

I made the final decision to apply the playbook. Claude Code helped me review the project and identify potential risks, but I kept control of the deployment.



---

**3. What evidence proves the VM is still reachable?**

I ran the Ansible ping command:

ansible web -i inventory.ini -m ping

The successful response "ping": "pong" confirmed that the VM was still reachable through SSH.

---

**4. Why should the risk review be run again after applying?**

The risk review should be run again to check the system after the changes have been applied. It helps identify any unexpected changes or new risks and confirms that the configuration is stable.

---

**5. What could go wrong if an AI agent applied Ansible changes automatically?**

An AI agent could make incorrect configuration changes, modify important files, cause service downtime, or affect the AWS infrastructure. Human approval helps prevent unexpected changes and keeps control of the deployment.

---

# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://lnkd.in/p/d_3UX4CR

---

#### Screenshot — Published LinkedIn post

![alt text](screenshots\linked-in-ass3-week9.png)

---

# Required Files

Confirm that the following files are included in your GitHub repository or assignment folder:

- [ ] `CLAUDE.md`
- [ ] `ansible-check-review.sh`
- [ ] `.claude/skills/ansible-risk-review/SKILL.md`
- [ ] `reports/risky-change-report.txt`
- [ ] `reports/post-apply-report.txt`
- [ ] `change-summary.md`

---

# Submission Instructions

- Add all required screenshots in your submission.
- Full Name must be visible in required screenshots and reports.
- All required notes must be answered clearly.
- Do not expose SSH private keys, passwords, cloud credentials, database credentials, or secret environment variables.
- Add your GitHub repository or folder URL inside this document.

---

# Completion Checklist

- [ ] Task 1: EpicBook connectivity confirmed and workspace created
- [ ] Task 2: `CLAUDE.md` created with safety rules
- [ ] Task 3: Claude Code produced a read-only risk-review plan
- [ ] Task 4: `ansible-check-review.sh` created and syntax checked
- [ ] Task 5: Baseline dry-run review completed
- [ ] Task 6: Claude Code `/ansible-risk-review` skill created and tested
- [ ] Task 7: Controlled risky change introduced and detected
- [ ] Task 8: Human applied the change and verified the result
- [ ] Risky-change report saved
- [ ] Post-apply report saved
- [ ] Change summary completed
- [ ] All screenshots added
- [ ] All notes answered
- [ ] LinkedIn post published
- [ ] LinkedIn post URL added
- [ ] No sensitive information exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra and The CloudAdvisory, focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## Resources

- DMI Official Website: [https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme)
- University: [https://university.pravinmishra.com?utm_source=github&utm_medium=readme](https://university.pravinmishra.com?utm_source=github&utm_medium=readme)
- Discord Community: [https://discord.pravinmishra.com?utm_source=github&utm_medium=readme](https://discord.pravinmishra.com?utm_source=github&utm_medium=readme)
- Blog: [https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*