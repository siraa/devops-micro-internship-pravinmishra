# Capstone Assignment — Deploy the Book Review App Using Terraform and Claude Code Agentic AI

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Student Details

**Full Name:** Add your full name here  
**Cloud Platform:** AWS or Azure  
**GitHub Repository URL:** Add your repository URL here  
**Public Application URL / Load-Balancer DNS:** Add the public URL or DNS here

---

## Purpose

Deploy the Book Review App using Terraform on AWS or Azure in a secure, highly available, production-style three-tier architecture. Use Claude Code, specialized subagents, Terraform MCP, and validation hooks to support the engineering workflow while keeping all infrastructure-changing operations under human control.

---

# Task 0 — Prepare the Project and Agentic AI Environment

## Goal

Prepare the Book Review App project and configure the provided Claude Code Agentic AI starter kit with project context, specialized subagents, Terraform MCP, validation hooks, and safety guardrails.

## Evidence

### Screenshot 1 — Project `CLAUDE.md`

Add a screenshot of the project `CLAUDE.md` showing the three-tier architecture, security boundaries, Terraform requirements, and human-approval rules.

![alt text](screenshots\sc1-T0-ass5-week8.png)

---

### Screenshot 2 — Terraform Engineer Subagent

Add a screenshot showing the Terraform Engineer subagent configuration.

![alt text](screenshots\sc2-T0-ass5-week8.png)

---

### Screenshot 3 — Architecture and Security Reviewer Subagent

Add a screenshot showing the Architecture and Security Reviewer subagent configuration.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 4 — Terraform MCP Connection

Add a screenshot showing Terraform MCP connected and available.

![alt text](screenshots\sc4-T0-ass5-week8.png)

---

### Screenshot 5 — Validation Hooks

Add a screenshot showing the configured Claude Code validation hooks.

![alt text](claudescreenshots\sc5-T0-ass5-week8.png)

---

# Task 1 — Design the Three-Tier Architecture

## Goal

<Design the required secure, highly available three-tier architecture and create an architecture diagram before building the infrastructure.

The diagram must show:

- VPC or VNet
- Availability Zones or equivalent availability locations
- Six subnets
- Internet connectivity
- NAT or outbound design
- Public load balancer
- Web Tier
- Internal load balancer
- Application Tier
- Managed MySQL
- Read replica
- Main traffic flow>

![alt text](screenshots\sc1-T0-ass1-week8.png)



#### Screenshot 5 — Public frontend load balancer configuration

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

#### Screenshot 6 — Internal backend load balancer configuration

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

#### Screenshot 7 — Healthy frontend and backend targets or backend pools

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

# Task 3 — VMs and Application Deployment

## Architecture Diagram

Add the completed architecture diagram here.

---

# Task 2 — Build the Terraform Networking and Security Layers


## Goal

Create the modular Terraform project and implement the network and security layers across the required public and private subnets.

## Evidence

### Screenshot 6 — Modular Terraform Project Structure

Add a screenshot showing the modular Terraform project structure.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 7 — Six-Subnet Architecture

Add a screenshot showing the six-subnet architecture across two availability locations.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 8 — Public and Private Tier Separation

Add a screenshot showing the public and private tier separation, including routing and security boundaries.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

# Task 3 — Build the Load-Balancing and Compute Layers

## Goal

Deploy the public and internal load balancers and the Web and Application compute resources required by the Book Review App.

## Evidence

### Screenshot 9 — Web and Application Compute

Add a screenshot showing the Web and Application compute resources in their required subnets.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 10 — Public Load Balancer

Add a screenshot showing the internet-facing public load balancer.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 11 — Internal Load Balancer

Add a screenshot showing the private internal load balancer.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---


#### Screenshot 14 — Functional review flow with working backend API and database integration
![alt text](screenshots\sc1-T0-ass1-week8.png)

### Screenshot 12 — Healthy Targets

Add a screenshot showing healthy target groups or backend pools.

![alt text](screenshots\sc1-T0-ass1-week8.png)


---

# Task 4 — Build the Managed MySQL Database Layer

## Goal

Deploy a private, highly available managed MySQL database with a read replica and restrict database connectivity to the Application Tier.

## Evidence

### Screenshot 13 — Managed MySQL Database

Add a screenshot showing the managed MySQL database deployment.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 14 — High Availability

Add a screenshot showing the Multi-AZ or high-availability configuration.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 15 — Read Replica

Add a screenshot showing the read replica configuration.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 16 — Private Database Access

Add a screenshot showing that the database is private and accepts MySQL traffic only from the Application Tier.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

# Task 5 — Validate, Review, and Apply the Terraform Configuration

## Goal

Validate the Terraform configuration, review the execution plan using both Agentic AI and human judgment, and apply the infrastructure changes only after all required checks pass.

## Evidence

### Screenshot 17 — Terraform Validation

Add a screenshot showing successful `terraform validate` output.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 18 — Terraform Plan

Add a screenshot showing the Terraform plan output.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 19 — Terraform Apply

Add a screenshot showing successful `terraform apply` completion.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

# Task 6 — Deploy and Configure the Book Review Application

## Goal

Deploy and configure the Book Review App across the Web, Application, and Database tiers and verify the complete application functionality.

## Evidence

### Screenshot 20 — Homepage

Add a screenshot showing the Book Review App homepage through the public endpoint.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 21 — Login or Authentication

Add a screenshot showing successful login or authentication.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 22 — Book Data

Add a screenshot showing the book listing or book details.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 23 — Review Functionality

Add a screenshot showing the review functionality working successfully.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 24 — Backend or API Evidence

Add a screenshot showing that the backend or API is working successfully.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 25 — Database Reads and Writes

Add a screenshot showing successful database reads and writes.

![alt text](screenshots\sc1-T0-ass1-week8.png)

## Public Application URL

**Public Application URL / DNS:** Add the working public application URL or load-balancer DNS here

---

# Task 7 — Demonstrate the Agentic AI Workflow

## Goal

Demonstrate how Claude Code assisted with Terraform generation, architecture and security review, and evidence-based troubleshooting while infrastructure-changing decisions remained under human control.

You do not need to submit your complete Claude Code conversation history. Include only focused evidence.

## Evidence

### Screenshot 26 — AI-Assisted Terraform Generation

Add a screenshot showing one useful example of AI-assisted Terraform generation or improvement.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 27 — Architecture or Security Review

Add a screenshot showing one structured architecture or security review result.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

### Screenshot 28 — AI-Assisted Troubleshooting

Add a screenshot showing one AI-assisted troubleshooting interaction based on collected evidence.

![alt text](screenshots\sc1-T0-ass1-week8.png)

---

# Task 8 — Complete the Final Architecture Review

## Goal

Review the completed infrastructure against the original capstone requirements and resolve significant architecture, security, reliability, and cost issues.

Confirm that the final review covers:

- Tier separation
- Availability
- Public exposure
- Routing
- Security rules
- Load balancing
- Database privacy
- Secrets
- Terraform quality
- Module structure
- Reliability
- Obvious cost risks

Use Screenshot 27 as the focused evidence for the structured architecture or security review.

---

# Task 9 — Answer the Reflection Questions

## Goal

Reflect on the architecture, Terraform implementation, and Agentic AI workflow. Answer each question briefly in your own words.

## Architecture

### 1. Why did you separate the Web, Application, and Database tiers?

I separated the three tiers to isolate responsibilities and improve security. Each tier has a specific role, and traffic can be controlled between the tiers.

### 2. Why is the Application Tier private?

The Application Tier is private because users should not access the backend directly. Requests go through the public Web Tier and the internal load balancer, which reduces the attack surface.

### 3. Why is MySQL private?

MySQL is private because the database should never be directly accessible from the Internet. Only the Application Tier should be allowed to connect to it on port 3306.

### 4. Why are multiple Availability Zones used?

Multiple Availability Zones improve availability. If one Availability Zone has a problem, resources in the other zone can continue serving the application.

### 5. What is the difference between Multi-AZ/high availability and a read replica?

Multi-AZ is mainly used for high availability and failover. A read replica is a separate database copy that can be used to handle read traffic and reduce the load on the primary database.

## Terraform

### 6. How did you divide your Terraform into modules?

I divided Terraform into separate modules for the main infrastructure components: network, security, alb, compute, and database. This makes the configuration easier to organize, reuse, and maintain.

### 7. How do the modules communicate through variables and outputs?

The root module passes configuration to child modules through variables. Each module exposes important resource information through outputs, which other modules can use as inputs.

### 8. What did you specifically check in `terraform plan`?

I checked which resources Terraform planned to create, modify, or destroy. I also checked subnet placement, security group rules, load balancer configuration, database settings, Availability Zones, and unexpected destructive changes before applying anything.  

## Agentic AI

### 9. What was the purpose of `CLAUDE.md`?

The purpose of CLAUDE.md was to provide Claude Code with persistent project-specific instructions and guardrails. It defined the expected AWS three-tier architecture, Terraform module structure, security requirements, validation workflow, and rules for human approval. It also instructed the Terraform Engineer to modify and validate Terraform code without automatically running destructive operations such as terraform apply or terraform destroy.

### 10. What work did the Terraform Engineer subagent perform?

The Terraform Engineer subagent was responsible for designing and working on the Terraform infrastructure for the Book Review App. It helped structure the infrastructure into modules for networking, security, load balancing, compute, and the database. It also helped configure the two-AZ architecture, public and private subnets, security groups, load balancers, EC2 instances, RDS MySQL, and the read replica. It was also used to validate Terraform configuration and troubleshoot infrastructure errors.

### 11. What did the Architecture and Security Reviewer identify?

The Architecture and Security Reviewer checked whether the infrastructure followed the required secure three-tier design. Important points included keeping the database in private subnets, preventing direct internet access to the application and database tiers, restricting MySQL access to the application tier, using separate security groups between tiers, and ensuring the load balancers were correctly positioned. The review also highlighted the importance of high availability across two Availability Zones and avoiding unrestricted SSH or MySQL access from 0.0.0.0/0.

### 12. Why did you use Terraform MCP instead of relying only on Claude's existing Terraform knowledge?

Terraform MCP was used to give Claude access to more accurate and current Terraform and infrastructure information instead of relying only on the model's general knowledge. This was useful for checking Terraform resources, provider behavior, configuration requirements, and infrastructure details. It reduced the risk of generating Terraform code based on outdated or incorrect assumptions. 

### 13. What was the purpose of your validation hooks?

The validation hooks were used as safety controls before infrastructure-changing operations. They ensured that Terraform code was formatted and validated and that a plan could be reviewed before applying changes. They also helped prevent unsafe operations such as automatic terraform apply, terraform destroy, or destructive changes without human approval.

### 14. Describe one real issue Claude helped you troubleshoot.

One real issue involved the RDS read replica. Terraform reported that replicate_source_db had to use the database ARN when a DB subnet group was specified. The configuration was using the primary database identifier instead. The issue was fixed by changing the replica configuration to use the primary RDS instance ARN, followed by terraform fmt, terraform validate, and terraform plan to verify the correction.

### 15. Describe one recommendation you reviewed, modified, or rejected instead of accepting blindly.

Architecture Diagram Description

Edge / Entry Point: Incoming web traffic reaches the Public IP configured on an external Azure Load Balancer (or Application Gateway).

Network Segmentation: Virtual Network (VNet) divided into distinct subnets:

Frontend Subnet: Houses the Load Balancer frontend configurations.

Backend Subnet: Isolated subnet housing a Virtual Machine Scale Set (VMSS) or Linux VMs guarded by a Network Security Group (NSG) allowing inbound traffic only from the Load Balancer.

Database / Backend Tier: Isolated private subnet holding backend data stores (e.g., Azure SQL / Database for MySQL) with Private Endpoints.


# Task 10 — Publish the Mandatory LinkedIn Post

## Goal

Publish a LinkedIn post describing the capstone, the technical work completed, the Agentic AI workflow, and the lessons learned.

Write the post in your own words, include at least one project image or other proof, and ensure that it can be viewed by the submission reviewer.

## LinkedIn Post URL

**LinkedIn Post URL:** https://lnkd.in/p/d2NjMSwh

---

# Submission Instructions

- Complete Tasks 0–10 in sequence.
- Include all Screenshots 1–28 exactly as specified.
- Ensure that your full name is visible in the required screenshots.
- Include the selected cloud platform.
- Include the completed architecture diagram.
- Include the modular Terraform project structure.
- Include the working public application URL or public load-balancer DNS.
- Include all required Agentic AI workflow evidence.
- Answer all 15 reflection questions briefly in your own words.
- Include the published LinkedIn post URL.
- Do not expose cloud credentials, database passwords, SSH private keys, JWT secrets, access tokens, account IDs, Terraform state containing sensitive values, or other confidential information.
- Review all screenshots and project files carefully before submitting through GitHub.

---

# Completion Checklist

- [ ] Selected AWS or Azure
- [ ] Added and reviewed the Agentic AI starter files
- [ ] Configured `CLAUDE.md`
- [ ] Configured the Terraform Engineer subagent
- [ ] Configured the Architecture and Security Reviewer subagent
- [ ] Connected Terraform MCP
- [ ] Configured validation hooks and safety guardrails
- [ ] Created the architecture diagram
- [ ] Created the six-subnet design
- [ ] Configured public Web Tier routing
- [ ] Kept the Application Tier private
- [ ] Kept the Database Tier private
- [ ] Configured tier-specific Security Groups or NSGs
- [ ] Restricted backend port `3001`
- [ ] Restricted MySQL port `3306` to the Application Tier
- [ ] Created the public load balancer
- [ ] Created the internal load balancer
- [ ] Configured listeners and health checks
- [ ] Deployed the Web Tier compute resources
- [ ] Deployed the private Application Tier compute resources
- [ ] Provisioned private managed MySQL
- [ ] Configured Multi-AZ or high availability
- [ ] Configured a read replica
- [ ] Created the modular Terraform project
- [ ] Used variables, outputs, and module dependencies
- [ ] Used current Terraform documentation through MCP
- [ ] Used hooks for deterministic validation
- [ ] Completed `terraform fmt`
- [ ] Completed `terraform validate`
- [ ] Reviewed `terraform plan`
- [ ] Completed the Terraform Engineer review
- [ ] Completed the Architecture and Security review
- [ ] Applied the infrastructure only after human approval
- [ ] Deployed and configured the backend
- [ ] Deployed and configured the frontend
- [ ] Configured Nginx where required
- [ ] Configured the internal backend endpoint
- [ ] Configured the public frontend endpoint
- [ ] Verified the homepage
- [ ] Verified login or authentication
- [ ] Verified book data
- [ ] Verified review functionality
- [ ] Verified the backend API
- [ ] Verified database reads and writes
- [ ] Verified healthy load-balancer targets
- [ ] Included AI-assisted Terraform generation evidence
- [ ] Included one architecture or security review
- [ ] Included one AI-assisted troubleshooting example
- [ ] Completed the final architecture review
- [ ] Answered all 15 reflection questions
- [ ] Published the mandatory LinkedIn post
- [ ] Added the LinkedIn post URL
- [ ] Captured all 28 required screenshots
- [ ] Confirmed that my full name is visible in the required screenshots
- [ ] Checked that no secrets or sensitive information are exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory), focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations through hands-on experience.

---

## Resources

- Book Review App Repository: [https://github.com/pravinmishraaws/book-review-app](https://github.com/pravinmishraaws/book-review-app)
- DMI Official Website: [https://dmi.pravinmishra.com](https://dmi.pravinmishra.com)
- University: [https://university.pravinmishra.com](https://university.pravinmishra.com)
- Discord Community: [https://discord.pravinmishra.com](https://discord.pravinmishra.com)
- Blog: [https://dmi.pravinmishra.com/blog](https://dmi.pravinmishra.com/blog)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra on LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory on LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of the DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
