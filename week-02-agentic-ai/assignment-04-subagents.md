# Assignment 4 — Building Your AI Team

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build and configure a set of specialized AI subagents inside your project. You will learn how different models and tool permissions define agent behavior, and you will trigger two real agent delegations to analyze security and cost aspects of your Terraform infrastructure.

---

# Task 1 — Create the Agents Folder and Add Files

## Goal

Create the `.claude/agents/` directory and add all required agent files.

### Evidence

#### Screenshot 1 — VS Code sidebar showing `.claude/agents/` with all 3 files


![alt text](screenshots\file-claude-agent.png)
---

# Task 2 — Compare the Agent Configurations

## Goal

Analyze the configuration differences between the three agents and demonstrate understanding of model and tool selection.

### Written Answers

#### 1. Why does the cost optimizer use Haiku instead of Sonnet?


The cost-optimizer agent uses Haiku instead of Sonnet for two main reasons:

Cost Efficiency: Haiku is significantly cheaper and faster than Sonnet. Since the agent's job is simply to scan text and look for cost-saving patterns (a relatively straightforward task), using a premium model like Sonnet would be overkill and expensive.

Task Complexity: Finding unused resources or matching text pattern optimizations doesn't require complex reasoning or advanced logic. Haiku's speed and capability are perfectly suited for basic file scanning, making it the most cost-effective tool for a cost-optimization role.

---

#### 2. Why does the security auditor NOT have Write in its tools list?


The security-auditor does NOT have Write privileges because of The Principle of Least Privilege: an auditor only needs to analyze and scan code, not modify it.

By keeping it read-only, you ensure Separation of Duties like flagging issues , fixing them 
and drastically reduce the Blast Radius—preventing the agent from accidentally deleting or altering your infrastructure files if something goes wrong.
---

#### 3. Why does the tf-writer use `inherit` instead of a specific model?

The tf-writer agent uses inherit so that it automatically adopts whichever model the parent agent (or the user) is currently running.

Here is why this is beneficial:

Flexibility: Instead of being locked into a single model, tf-writer dynamically scales. If you are running a cheap model for quick tasks, it stays cheap. If you switch to a powerful model (like Sonnet) for complex infrastructure architecture, tf-writer automatically upgrades to that same model to write higher-quality code.

Consistency: It ensures that the agent analyzing your request and the agent writing the code are on the exact same page, utilizing the same underlying intelligence level.

---

### Evidence

#### Screenshot 2 — `security-auditor.md` frontmatter showing model and tools configuration



![alt text](screenshots\security-auditor.png)
---

#### Screenshot 3 — `cost-optimizer.md` frontmatter showing the model and tools configuration


![alt text](screenshots\cost-optimiser.png)
---

# Task 3 — Run the Security Auditor

## Goal

Trigger the security auditor agent and analyze the generated security report for your Terraform infrastructure.

### Evidence

#### Screenshot 4 — The delegation message showing Claude launched the security-auditor


![alt text](screenshots\delegation-security.png)
---

#### Screenshot 5 — Security audit report output


![alt text](screenshots\audit-report-output.png)
---

# Task 4 — Run the Cost Optimizer

## Goal

Trigger the cost optimizer agent and review the generated cost optimization report.

### Evidence

#### Screenshot 6 — The full cost optimization report



![alt text](screenshots\coast-optimisation-1.png)

![alt text](screenshots\coast-optimisation-2.png)

---

# Submission Instructions

- Ensure all agent files are committed in `.claude/agents/`
- Complete all written answers in your GitHub Repo
- Push final changes to your forked GitHub repository

---

## GitHub Repository URL

Paste your forked repository URL here:

<<<<<<< HEAD
https://github.com/siraa/Ultimate-Agentic-DevOps-with-Claude-Code


=======

>>>>>>> upstream/main

---

# Completion Checklist

- [ ] `.claude/agents/` folder contains all 3 agent files
- [ ] Screenshot 2 shows correct `security-auditor.md` configuration
- [ ] Screenshot 3 shows correct `cost-optimizer.md` configuration
- [ ] All 3 written answers completed 
- [ ] Security auditor executed successfully
- [ ] Cost optimizer executed successfully
- [ ] Security report is visible with findings
- [ ] Cost report is visible with recommendations
- [ ] All required screenshots added
- [ ] GitHub repo updated with agents

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