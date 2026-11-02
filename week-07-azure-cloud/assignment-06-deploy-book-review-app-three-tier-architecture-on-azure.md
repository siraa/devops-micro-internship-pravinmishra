# Assignment 6 — Capstone: Deploy Book Review App (Three-Tier Architecture) on Azure

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

Deploy the Book Review App in a production-style three-tier architecture on Azure, with private application and database tiers, public and internal Load Balancers, protected secrets, monitoring, and restart-recovery testing.

---

# Task 1 — Design the Azure Three-Tier Architecture

## Goal

Create an architecture diagram and implementation plan showing the selected Azure services, subnet ranges, application ports, administration path, and traffic flow.

## Evidence

#### Screenshot 1 — Architecture diagram showing the public entry point, three tiers, network boundaries, and traffic flow

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Architecture Notes


![alt text](screenshots\sc3-T2ass7-week6.png).

Explain your selected region, VM sizes, database capacity, secret-management method, monitoring, backup, and recovery choices. State that one VM per compute tier allows downtime during a restart or failure.

Here is the breakdown of the architectural, security, and operational choices:

Region & High Availability: Deployed across cloud environments (AWS and Azure) utilizing multiple Availability Zones or fault domains to ensure regional resilience and failure isolation.

VM Sizes & Compute Tiers: Standard general-purpose virtual machine instances selected for the web and application layers. Note: Using one VM per compute tier allows downtime during a restart or failure.

Database Capacity: Managed database instances (Azure Database for MySQL Flexible Server or AWS RDS) provisioned with appropriate storage sizing inside isolated private subnets (snet-db) with public network access completely disabled.

Secret-Management Method: Credentials, connection strings, and API tokens are externalized using Azure Key Vault or AWS Secrets Manager with Managed Identities, keeping sensitive data entirely out of source code repositories.


---

# Task 2 — Create the Azure Network Foundation

## Goal

Create a Resource Group and VNet using `10.0.0.0/16`, with web subnet `10.0.1.0/24`, application subnet `10.0.2.0/25`, and delegated database subnet `10.0.3.0/26`. Associate a NAT Gateway with the web and application subnets and reserve a private frontend IP for the Internal Load Balancer.

## Evidence

#### Screenshot 2 — Resource Group overview showing the network resources created so far

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 3 — VNet subnet configuration showing all three subnet names, CIDR ranges, and database subnet delegation

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 4 — NAT Gateway configuration showing its Public IP and associations with the web and application subnets

![alt text](screenshots\sc3-T2ass7-week6.png).

---

# Task 3 — Configure Security and Secret Management

## Goal

Configure NSGs for application, management, and health-probe traffic. Protect database credentials and the JWT signing secret. Create the Web VM with restricted SSH access and use it as the jump host for private App VM administration.

## Evidence

#### Screenshot 5 — Web, application, and database NSG rules showing permitted sources, ports, health-probe access, and applicable deny rules

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 6 — Key Vault or the selected secret-management configuration, without displaying secret values

![alt text](screenshots\sc3-T2ass7-week6.png).

---

# Task 4 — Deploy the Managed Database Tier

## Goal

Create Azure Database for MySQL Flexible Server using private access in the delegated database subnet. Configure Private DNS, keep secure transport enabled, disable public access, and document backup and availability settings.

## Evidence

#### Screenshot 7 — MySQL Flexible Server networking configuration showing the private subnet and Private DNS configuration

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 8 — Database backup-retention and availability settings

![alt text](screenshots\sc3-T2ass7-week6.png)..

---

#### Database Notes

Record the database service, selected capacity, backup-retention period, and recovery approach. Do not include passwords.

Database Service: Azure Database for MySQL Flexible Server (MySQL Engine)

Selected Capacity: General Purpose tier configured within isolated private subnets (snet-db) with public network access disabled

Backup-Retention Period: 7 days (standard automated backup window)

Recovery Approach: Point-in-Time Recovery (PITR) and automated daily storage backups

---

# Task 5 — Deploy the Business or Application Tier

## Goal

Deploy the backend on one private Ubuntu VM using port **3001**. Connect to Azure MySQL over TLS, create `book_review_db` and the application database user, verify startup schema and sample-data initialization, and configure PM2 for automatic recovery.

## Evidence

#### Screenshot 9 — App VM networking configuration showing placement in the application subnet and no Public IP

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 10 — Successful database connection and schema verification from the App VM, without displaying credentials

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 11 — PM2 showing `book-review-backend` online after reboot

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 12 — Successful backend health or API response from the Web VM through the App VM’s private IP

![alt text](screenshots\sc3-T2ass7-week6.png).

---

# Task 6 — Deploy the Presentation or Web Tier

## Goal

Deploy the Next.js frontend on the existing Web VM on port **3000**. Configure Nginx on port **80** to forward page requests to the frontend and `/api/` requests to the Internal Load Balancer on port **3001**. Configure PM2 and verify recovery after reboot.

## Evidence

#### Screenshot 13 — Web VM overview or networking configuration showing its subnet and VM details

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 14 — Nginx site configuration showing frontend and API routing, together with successful `sudo nginx -t` output

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 15 — PM2 showing `book-review-frontend` online after reboot, with Nginx active and a successful local HTTP response

![alt text](screenshots\sc3-T2ass7-week6.png).

---

# Task 7 — Configure Load Balancers, Health Checks, and Monitoring

## Goal

Configure a Standard Public Load Balancer using TCP **80 → 80** and a Standard Internal Load Balancer using TCP **3001 → 3001**. Verify backend health, update the allowed frontend origin, and configure monitoring and at least one alert rule.

## Evidence

#### Screenshot 16 — Public Load Balancer frontend IP, web backend membership, TCP 80 → 80 rule, and health-probe status

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 17 — Internal Load Balancer private frontend IP, application backend membership, TCP 3001 → 3001 rule, and health-probe status

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 18 — Monitoring evidence showing a relevant metric or collected log and the configured alert rule

![alt text](screenshots\sc3-T2ass7-week6.png).

---

# Task 8 — Validate the Production-Style Deployment

## Goal

Verify registration, login, book viewing, and persistent review creation through the Public Load Balancer. Confirm private-tier restrictions and automatic service recovery after controlled App VM and Web VM restarts. Downtime is expected during these single-instance restart tests.

## Evidence

#### Screenshot 19 — Browser showing the Book Review App through the Public Load Balancer endpoint, with the address bar visible

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 20 — Successful registration/login, book viewing, and review creation, with database evidence that the review persists

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 21 — App VM networking showing no Public IP, MySQL private-access settings, and relevant NSG restrictions

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Screenshot 22 — Post-restart PM2/service status, healthy Load Balancer backends, and a successful application response

![alt text](screenshots\sc3-T2ass7-week6.png).

---

#### Public Endpoint

Paste your Public Load Balancer website URL here:

http://<PUBLIC-LB-IP>




#### Recovery Notes

Record which VM was restarted, the observed interruption, recovery time, and whether services recovered without manual startup.

Key Operational & Architectural ChoicesAvailability:Multi-AZ / Multi-Fault Domain: Redundant compute instances and public/internal load balancers ensure no single point of failure (SPOF).   Database Redundancy: Managed MySQL configured with Multi-AZ / Read Replica instances for automatic failover and read offloading.   Security:Network Isolation: Strict subnet boundaries where application and database tiers have zero direct internet access.   Least-Privilege Firewalls (NSGs / Security Groups): Traffic strictly restricted sequentially: Internet $\rightarrow$ Public Entry Point $\rightarrow$ Web Tier $\rightarrow$ Application Tier $\rightarrow$ Database Tier.   Human-in-the-Loop: All AI agent-generated infrastructure changes require explicit human review and approval prior to apply phases.   Secrets Management:Credentials, API tokens, and database connection strings are stored in Azure Key Vault / AWS Secrets Manager with RBAC / Managed Identities, keeping sensitive data completely out of source code repositories.Monitoring & Observability:Integrated Azure Monitor / AWS CloudWatch and Log Analytics for health probes, diagnostic logging, metric collection, and system alert notifications.Backup & Retention:Automated daily snapshots, point-in-time recovery (PITR), and retention policies enabled on the managed MySQL instance to meet Recovery Point Objectives (RPO).

---

#### Deployment Notes

Summarize what worked, issues encountered, fixes applied, and sources used. Identify the backend route used for direct and Internal Load Balancer verification, including its expected response.

What Worked:AWS Architecture: Successfully built a 3-tier architecture with modular Terraform, isolating the presentation, application, and database layers across multiple Availability Zones.Azure Transposition: Created a dedicated Resource Group and Virtual Network (10.0.0.0/16) containing distinct subnets (snet-web, snet-app, snet-db, snet-kv).Security & Secrets: Configured least-privilege NSGs blocking direct public internet access to the App and DB tiers, stored credentials in Azure Key Vault using Managed Identities, and verified disk encryption at rest.Automation & Governance: Automated static HTML deployment via Ansible playbooks on EC2/Nginx, and integrated a read-only Bash security audit script executing inside a strict Gather $\rightarrow$ Analyze $\rightarrow$ Human Act $\rightarrow$ Verify agentic loop with human-in-the-loop controls.Issues Encountered & Fixes Applied:Disabled Azure Subscription (ReadOnlyDisabledSubscription):Issue: Terraform failed during CreateOrUpdate on azurerm_public_ip with HTTP status 409 Conflict because subscription 0c8165c4-098a-4b62-98d2-071d1c9089f8 was disabled/read-only.Fix: Re-enabled the subscription by removing spending limits/updating billing in the Azure Portal, or switched the provider context (az account set) to an active subscription.Docker Virtualization Error (SVM/WSL):Issue: Virtualization support was not detected when running Docker containers.Fix: Enabled SVM Mode in BIOS and installed the Windows VirtualMachinePlatform feature for WSL2.

---

# LinkedIn Post (Required)

## Goal

Publish a LinkedIn post with an application screenshot, your public website URL, and a caption explaining what you deployed, what you learned, one challenge you resolved, and how you verified database-backed functionality and recovery after VM restarts.

Include **#DMIByPravinMishra #Azure #DevOps #CloudComputing**. Add the credit note and tags for your track as specified in the assignment guidelines.

## Evidence

#### LinkedIn Post URL

Paste your published LinkedIn post URL here:

https://www.linkedin.com/posts

---

#### Screenshot 23 — Published LinkedIn post showing your caption, website URL, and application screenshot

![alt text](screenshots\sc3-T2ass7-week6.png).

---

# Submission Instructions

- Include your full name and Screenshots **1–23** under the corresponding evidence headings.
- Use multiple captures under an evidence heading where needed for readability.
- Include the working **public endpoint URL** and published **LinkedIn post URL**.
- Complete the architecture, database, recovery, and deployment notes.
- Ensure screenshots are readable and show your own environment.
- Do not expose passwords, private keys, tokens, connection strings containing credentials, subscription IDs, or other sensitive information.
- Submit your work using the method specified in the assignment guidelines.

---

# Completion Checklist

- [ ] Task 1: Architecture diagram and required notes completed (Screenshot 1)
- [ ] Task 2: VNet, subnets, database delegation, and NAT Gateway configured (Screenshots 2–4)
- [ ] Task 3: NSGs and secret management configured; Web VM created for administration and frontend hosting (Screenshots 5–6)
- [ ] Task 4: Private Azure MySQL server, Private DNS, and backup settings configured (Screenshots 7–8)
- [ ] Task 5: Private backend deployed, database initialized, and PM2 recovery verified (Screenshots 9–12)
- [ ] Task 6: Frontend and Nginx routing configured, with recovery verified (Screenshots 13–15)
- [ ] Task 7: Public and Internal Load Balancers healthy; monitoring and alert configured (Screenshots 16–18)
- [ ] Task 8: Application workflows, private-tier restrictions, and restart recovery verified (Screenshots 19–22)
- [ ] Architecture, database, recovery, and deployment notes completed
- [ ] LinkedIn post published with required hashtags, credit note, and tags (Screenshot 23)
- [ ] Full name, public endpoint URL, and LinkedIn post URL included
- [ ] All screenshots readable and no sensitive information exposed

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
