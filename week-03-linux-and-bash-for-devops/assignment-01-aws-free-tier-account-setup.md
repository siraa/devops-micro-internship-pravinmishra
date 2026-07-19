# Assignment 1 — AWS Free Tier Account Setup (EpicReads Cloud Onboarding)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will create and verify an AWS Free Tier account as part of onboarding EpicReads — an online bookstore moving to the cloud. You will demonstrate an understanding of AWS fundamentals, Free Tier services, and account setup by answering conceptual questions and capturing proof of a working AWS Console login.

---

# Task 1 — Understanding AWS & Free Tier

## Goal

Demonstrate understanding of AWS basics and Free Tier usage by answering the following questions in your own words (3–4 lines each).

### Answers

#### Question 1 — What is an AWS account, and why do you need it at this stage?

Write your answer here.

An AWS account is our secure sandbox on Amazon's cloud platform, giving us on-demand access to virtual servers, storage, databases, and advanced networking tools.
At this stage in our DevOps journey, it is an absolute necessity for this reasons:

Moving from Theory to Production: It is the live environment where we code takes form. Without it, we cannot execute terraform apply to deploy real S3 buckets, CloudFront distributions, or infrastructure.

Testing Real Security and IAM Roles: 
It allows us to practice cloud governance by 
configuring real Identity and Access Management (IAM) policies, ensuring your pipelines or autonomous agents operate strictly under the principle of least privilege.

Mastering Real-World Troubleshooting: It forces us to deal with actual cloud networking realities. We need it to validate that your inbound rules are set up correctly, the right ports are open, and our deployment can communicate with the outside world.

Why you need AWS:
By hosting your Nginx server on AWS, you get access to tools like Amazon CloudWatch. You can practice setting up automated alerts that ping you if the server crashes, if CPU usage spikes, or if malicious traffic hits your environment. 
In short, an AWS account is where our static configuration files become a live, functional ecosystem.

---

#### Question 2 — What is AWS Free Tier, and how long does it last?

Write your answer here.

The AWS Free Tier lets you use cloud services for free up to certain monthly limits.
It has three types of timelines:

12 Months Free: Expired exactly 1 year after you create your account (e.g., covers EC2 t2.micro and 5 GB of S3 storage).

Always Free: Never expires, available indefinitely (e.g., covers AWS Lambda and DynamoDB limits).

Short-Term Trials: Lasts only 30 to 90 days depending on the specific service.

---

#### Question 3 — Name three AWS Free Tier services and their free usage limits.

Write your answer here.
1. Amazon EC2 (Elastic Compute Cloud)
Offer Type: 12 Months Free
Limit: 750 hours per month
Scope: Applies to t2.micro or t3.micro instances (dependent on region) for Linux or Windows configurations. Hours accumulate across all concurrent running instances.
2. Amazon S3 (Simple Storage Service)
Offer Type: 12 Months Free
Limit: 5 GB Standard Storage
Scope: Includes 20,000 GET requests and 2,000 PUT requests per month.
3. AWS Lambda
Offer Type: Always Free
Limit: 1 million requests per month
Scope: Includes 400,000 GB-seconds of compute time per month, determined by function memory allocation and execution duration.

---

# Task 2 — Create AWS Free Tier Account

## Goal

Create a valid AWS Free Tier account and sign in to the AWS Management Console.

> No screenshots required for this task. Completion is verified through Task 3.

---

# Task 3 — Verify AWS Account

## Goal

Confirm that your AWS account setup is complete by navigating to the Account section and capturing proof.

### Evidence

#### Screenshot 1 — AWS Account page showing account name (email may be blurred)



![alt text](screenshots\AWS-Account-page.png)
---

# Submission Instructions

- Add all required screenshots in your GitHub repository submission
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [ ] Task 1 answers written in own words
- [ ] AWS Free Tier account created successfully
- [ ] Signed in to AWS Management Console
- [ ] Screenshot of AWS Account page captured (full name visible, no sensitive data)
- [ ] All required screenshots added to repository

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