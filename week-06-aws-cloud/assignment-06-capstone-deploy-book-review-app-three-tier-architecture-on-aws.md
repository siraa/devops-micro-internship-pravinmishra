# Assignment 6 — Capstone Assignment — Deploy Book Review App (Three-Tier Architecture) on AWS

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

This is the most important assignment of the course. You will deploy the Book Review App in a fully production-style three-tier architecture on AWS: a Next.js Web Tier behind Nginx and a public ALB, a private Node.js/Express App Tier behind an internal ALB, and a private Multi-AZ MySQL RDS database with a read replica. You are expected to design, deploy, isolate, debug, and document the result independently.

---

# Task 1 — Architecture Diagram

## Goal

Create an architecture diagram showing the custom VPC (10.0.0.0/16), the six subnets across two Availability Zones (two public Web Tier, two private App Tier, two private Database Tier), the public ALB, Web Tier EC2/Nginx, internal ALB, private App Tier EC2, private Multi-AZ RDS with its read replica, and the permitted traffic flow.

### Evidence

#### Diagram image or link

![alt text](screenshots\sc1-T1-ass6-week6.png).

---

# Task 2 — AWS Region & Services Used

## Goal

Record the AWS Region used and list every AWS service used across networking, compute, load balancing, security, and the database.

### Notes

**Region:**

eu-central-1

---

**Services:**

Networking & Content Delivery	Amazon VPC (Public/Private Subnets), NAT Gateway, Elastic IP (EIP)
Compute	Amazon EC2 (t3.micro), EC2 Auto Scaling Groups (ha-asg), EC2 Launch Templates (ha-web-lt)
Load Balancing	Application Load Balancer (ha-alb), Target Groups (ha-web-tg)
Security & Identity	Security Groups, AWS Systems Manager (SSM / Session Manager), AWS IAM
Database	Amazon RDS (MySQL)

---

# Task 3 — Public Entry Point

## Goal

Confirm the Book Review App loads through the public ALB DNS name.

### Evidence

#### Public ALB DNS

Paste your public ALB DNS name here:

https://public-web-alb-219662638.us-east-1.elb.amazonaws.com/

---

# Task 4 — Evidence Screenshots

## Goal

Capture visual proof of every tier and load balancer.

### Evidence

#### Web EC2

![alt text](screenshots\sc12-T5-ass4-week6.png).

---

#### App EC2

![alt text](screenshots\sc12-T5-ass4-week6.png).

---

#### Public ALB

![alt text](screenshots\sc12-T5-ass4-week6.png).

---

#### Internal ALB

![alt text](screenshots\sc12-T5-ass4-week6.png).

---

#### RDS + Replica

![alt text](week-06-aws-cloud\screenshots\read-replica.png).

---

#### App UI proof

![alt text](screenshots\app-ui-ass6-week6.png).

---

# Task 5 — Summary

## Goal

Summarize what worked in the final deployment, the issues encountered and how each was fixed, and the tools or sources used to research and debug.

### Notes

**What worked:**

The final deployment successfully provisioned an automated, highly available web architecture running on Amazon Web Services (AWS) in the eu-central-1 (Frankfurt) region.The successful architecture consists of:Networking & Isolation: A custom Amazon VPC configured with multi-AZ public subnets (for external load balancing) and private subnets (for compute and database layers). Outbound internet connectivity from private subnets is enabled via a NAT Gateway paired with an Elastic IP (EIP).Compute & Auto-Scaling: An EC2 Auto Scaling Group (ha-asg) running t3.micro instances across Availability Zones (eu-central-1a, eu-central-1c). Web server instances automatically initialize and enable their web services (httpd/apache2) upon launch using an updated EC2 Launch Template (HA-WEB-Launch-Template v2) User Data script.Load Balancing & Traffic Distribution: An Application Load Balancer (ha-alb) listening on public HTTP port 80, tied to an AWS Target Group (ha-web-tg) that continuously monitors instance health and routes inbound traffic exclusively to healthy private EC2 targets.Security Scaffolding: Layered Security Groups restricting traffic progression (Internet $\rightarrow$ ALB on port 80 $\rightarrow$ EC2 instances on custom application ports/HTTP $\rightarrow$ RDS Database on port 3306). Remote access and administration are handled without public IP exposure or open SSH ports via AWS Systems Manager (SSM / Session Manager) attached via IAM instance profiles.Database Layer: A managed Amazon RDS MySQL instance running in private subnets, storing backend application data for the deployment.

---

**Issues + fixes:**

Local SSH/SCP Syntax Errors

Issue: Running ssh -i "C:\...\ha-cloud.pem"ubuntu@... returned No such file or directory because the missing space caused SSH to append the username directly to the file path.

Fix: Added proper spacing between the identity file path and the target destination (ssh -i ").

Direct SSH Connections to Private Instances

Issue: Attempting to SSH directly into private IP addresses  from a local PC timed out because private subnets are non-routable over the public internet.

Fix: Implemented a two-hop Bastion setup. Uploaded ha-cloud.pem to the public Web EC2 via scp, SSH'd into the Web EC2, set key permissions (chmod 400 ha-cloud.pem), and initiated the second SSH hop internally from the Web EC2 to the private App EC2.

Executing Local Commands on Remote Hosts

Issue: Running chmod 400 C:\Users\... or looking for .pem files inside the remote Ubuntu EC2 shell failed because the key file resided strictly on the local Windows machine.

Fix: Separated environment contexts—executed local file transfers on Windows PowerShell/Git Bash and reserved remote Linux terminal sessions for server administration tasks.

---

**Tools/sources used:**

Command Line Utilities & Terminal Clients:

Git Bash & Windows PowerShell: Local shell environments used to execute file transfers (scp) and establish initial SSH sessions.

OpenSSH (ssh, scp): Native tools used for secure key-authenticated remote shell connections and Bastion host file transfers.

Netcat (netcat-openbsd): Used to perform network port scans and verify raw TCP line-of-sight connectivity (nc -zv) from EC2 to the RDS database on port 3306.

MySQL Client (mysql): Command-line client installed on Ubuntu to inspect database schemas, verify credentials, and test SQL queries.

Git: Distributed version control tool used to clone the application backend repository (git clone).

---

# LinkedIn Post (Required)

## Goal

Publish a LinkedIn post sharing the capstone deployment, including the public ALB DNS (or a redacted screenshot), three to five lines on what you built and why it is production-style, and one proof screenshot.

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/taysir-ouaslati-9b6527a3_ust-deployed-a-fully-automated-multi-az-activity-7498313540533972992-R5SB?utm_source=share&utm_medium=member_desktop&rcm=ACoAABX4AtoB0tpceeC8Jnqozhzdi2ViZ02bFHk

---

#### Screenshot of LinkedIn post

![alt text](screenshots\linked-infoto-ass6-week6.png).
---

# Submission Instructions

- Add all required screenshots and links in your submission
- Do not expose passwords, RDS credentials, connection strings, private keys, or account IDs

---

# Completion Checklist

- [ ] Task 1: Architecture diagram completed
- [ ] Task 2: AWS Region and services documented
- [ ] Task 3: Public ALB DNS confirmed working
- [ ] Task 4: All six evidence screenshots captured (Web Tier, App Tier, both ALBs, RDS + replica, app UI)
- [ ] Task 5: Deployment summary completed (what worked, issues/fixes, tools/sources)
- [ ] LinkedIn post published and URL submitted
- [ ] App Tier and Database Tier confirmed not publicly accessible
- [ ] No sensitive data exposed

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