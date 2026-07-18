# Assignment 3 — Production Maintenance Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will treat your already deployed React application (on Ubuntu VM with Nginx) as a live production system. You will perform structured operational checks covering network validation, service health, log analysis, resource monitoring, configuration verification, and incident simulation with recovery — mirroring real on-call DevOps responsibilities.

---

# Task 1 — Server Access & Networking Validation

## Goal

Verify that the deployed React application is reachable from the browser and confirm basic network connectivity of the Ubuntu VM.

### Evidence

#### Screenshot 1 — Browser showing the React app with your Full Name visible on the UI

Add your screenshot here.

---

#### Screenshot 2 — Output of `ip a`

Add your screenshot here.

---

#### Screenshot 3 — Output of `sudo ss -tulpen`

Add your screenshot here.

---

#### Screenshot 4 — Output of `sudo ufw status`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What proves Nginx is listening on 0.0.0.0:80?**

Write your answer here.

To verify and prove that the Nginx web server is actively listening on port 80 across all IPv4 interfaces (0.0.0.0), we can analyze the network socket state using the ss command: Output of `sudo ufw status`
The Binding IP (0.0.0.0): The local address is bound to 0.0.0.0, which represents the IPv4 wildcard address. This proves that Nginx is not restricted to the local loopback (127.0.0.1) but is actively listening on all available network interfaces of the EC2 instance (including the private IP mapped to our public AWS IP).

The Port (:80): The socket configuration explicitly designates port 80, confirming that standard HTTP traffic is being targeted.

The State (LISTEN): The socket state is marked as LISTEN, which proves the port is open and actively waiting to accept incoming client connections.

The Process Name (nginx): The process column explicitly lists "nginx" (along with its PID and file descriptors), confirming that the Nginx master/worker processes are the owners of this socket.


Bash
---

**2. What proves SSH is active on port 22?**

Write your answer here.
To prove that the SSH service (typically managed by sshd) is active and listening on port 22, you can use two main technical proofs from our Ubuntu terminal:

Proof 1: Network Socket Verification (Active Listener)
When we run the system socket utility command: sudo ss -tulpen
and we are looking for a line in the output that matches this pattern:
tcp         LISTEN       0            4096                              [::]:22                         [::]:*           users:(("sshd",pid=0000,fd=4),("systemd",pid=1,fd=145))  

How this proves SSH is active:

0.0.0.0:22 (and/or [::]:22): This indicates the process is binding to port 22 on all IPv4 addresses (0.0.0.0) and IPv6 addresses ([::]).

LISTEN: This state proves the port is actively open and waiting for incoming connection requests.

sshd: The process column explicitly identifies "sshd" (Secure Shell Daemon) as the owner of this socket.
Proof 2: Service Status Verification (Systemd)
You can check the operational health of the SSH daemon itself by running:sudo systemctl status ssh

What to look for in the output:

Active: active (running) (in green text) confirms the daemon is fully operational.

Server listening on 0.0.0.0 port 22. and Server listening on :: port 22. will appear in the recent log snippets at the bottom of the output, explicitly stating which

Bash
Bash
---

**3. Did you find any unexpected open ports? Explain briefly.**

Write your answer here.

No unexpected open ports were found.

A security audit of the listening sockets (sudo ss -tulpen) reveals a highly restricted and secure baseline posture, with only the expected system services active:

Port 22 (sshd): Expected and required for secure, remote administrative access via SSH.

Port 80 (nginx): Expected and required to serve our React web application to public HTTP traffic.

Port 53 / 127.0.0.53 (systemd-resolved): Expected. This is local to the loopback interface only and is Ubuntu's internal, system-level DNS resolver used to translate domain names for outgoing server requests. It is not exposed to the internet.

Because there are no unmapped, high-number ports (e.g., development database ports like 5432 or 3306, or unexpected backdoors) listening on public interfaces (0.0.0.0), the system adheres to the DevOps principle of least privilege at the network level. This local configuration perfectly mirrors our external AWS Security Group rules.
---

# Task 2 — Service Health & Systemd Validation (Nginx)

## Goal

Verify that Nginx is properly installed, running, enabled at boot, and safely configured.

### Evidence

#### Screenshot 1 — Output of `systemctl status nginx --no-pager`

Add your screenshot here.

![alt text](screenshots\system-ctl-week3.png)
---

#### Screenshot 2 — Output of `sudo nginx -t`

Add your screenshot here.

![alt text](screenshots\sc2-T2-week3.png)
---

#### Screenshot 3 — Output of `sudo ss -lptn '( sport = :80 )'`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What happens if Nginx fails to restart in production?**

Write your answer here.

If Nginx fails to restart in a production environment, the operational and technical impact depends entirely on the deployment strategy used to apply the configuration changes:

1. The Technical Impact: Outage vs. Safe Fallback
If using systemctl restart nginx (High Risk):
This command immediately terminates all running Nginx master and worker processes before attempting to spin up new ones. If the new configuration contains a syntax error or a port conflict, the service will fail to initialize. As a result, Nginx goes completely offline, leaving port 80 (HTTP) and 443 (HTTPS) closed. Any client attempting to access the React application will receive an immediate ERR_CONNECTION_REFUSED browser error, causing a total production outage.
---

**2. What's your basic rollback plan?**

Write your answer here.

Our basic rollback plan relies on pre-emptive backups, declarative configuration validation, and zero-downtime reloads. Instead of scrambling to rewrite files during an incident, we instantly restore a pre-validated snapshot of the configuration.
Rule of the Timestamped Backup: Never edit a configuration file directly.
Rule of Reload over Restart: Never use systemctl restart to apply or rollback changes. Always use reload to maintain active client traffic without dropping packets.
like sudo systemctl restart nginx
---

# Task 3 — Logs & Request Trace

## Goal

Verify real traffic flow and analyze logs to understand system behavior and errors.

### Evidence

#### Screenshot 1 — Output of `sudo tail -n 30 /var/log/nginx/access.log`

Add your screenshot here.

---

#### Screenshot 2 — Output of `sudo tail -n 30 /var/log/nginx/error.log`

Add your screenshot here.

---

#### Screenshot 3 — Output of `sudo journalctl -u nginx --no-pager -n 50`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. Were there any errors in the logs?**

- If yes, mention 1–2 example error lines from the logs and explain what each one means in simple terms.
- If no, explain what it means if the error log is empty or shows no recent errors during your check.

Write your answer here.

No, there were no active errors in the log.

During our diagnostic check of /var/log/nginx/error.log, the log returned empty (or showed only historical start-up notices with no [error] or [crit] levels).

What this means for our system:

Healthy Runtime State: This proves Nginx is operating in a healthy state, successfully locating and serving the compiled static React assets (HTML, JS, CSS) without running into missing files or path resolution issues.
---

**2. If there were no errors, what does that indicate about the system?**

Write your answer here.

The absence of errors in the /var/log/nginx/error.log file indicates that the web server and the host system are operating in a perfectly healthy, stable, and highly secure baseline state.
An empty log confirms there are no server-side upstream failures, gateway bottlenecks, or socket communication issues. The server is delivering static assets efficiently without dropouts, meaning all client connections are resolving cleanly (which are logged as successful traffic in access.log rather than error.log).
---

**3. Based on the access logs, were your curl requests visible in the log entries? What does that prove about traffic flow?**

Write your answer here.
Yes, the curl requests were fully visible in the Nginx access log entries.

1. The Evidence: Analyzing a curl Log Entry
When we execute a local or remote curl request (for example, curl -I [http://127.0.0.1:80](http://127.0.0.1:80) or curl [http://@ip](http://@ip)), Nginx records a line in /var/log/nginx/access.log using the standard combined log format.
---

# Task 4 — System Resource Health Check (Capacity Red Flags)

## Goal

Assess server capacity and detect potential performance or failure risks.

### Evidence

#### Screenshot 1 — Output of `uptime`

Add your screenshot here.

---

#### Screenshot 2 — Output of `free -h`

Add your screenshot here.

---

#### Screenshot 3 — Output of `df -h`

Add your screenshot here.

---

#### Screenshot 4 — Output of `sudo du -sh /var/* | sort -h`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. Which resource looks most critical right now? (CPU/load, memory, or disk) Explain why.**

Write your answer here.

In our current AWS EC2 Ubuntu deployment, Memory (RAM) is identified as the most critical resource.

While the system is currently stable, an analysis of our system metrics reveals that memory represents our primary bottleneck and operational risk, whereas CPU and Disk resources maintain comfortable margins of safety.
CPU / Load	Very Low	Non-Critical. Nginx serves compiled, static React frontend assets directly from disk. This operation requires minimal CPU processing. Idle/steady-state CPU utilization typically remains under 5%.
also CPU / Load Average: uptime (To inspect the system load averages over 1, 5, and 15 minutes against our available vCPUs).
---

**2. What happens if disk becomes 100% full in a production server?**

Write your answer here.

When a production server's root disk (/) reaches 100% utilization, the system loses its ability to perform write operations (write()). This leads to immediate operational instability, service failures, and data risks across multiple layers of the application stack.
---

# Task 5 — Configuration & Deployment Verification

## Goal

Ensure the correct React build is deployed and Nginx is serving it properly.

### Evidence

#### Screenshot 1 — Output of `ls -lah /var/www/html | head -n 20`

Add your screenshot here.

---

#### Screenshot 2 — Output of ` `

Add your screenshot here.

---

#### Screenshot 3 — Output of `grep -n "try_files" /etc/nginx/sites-available/default`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. How do you confirm that the correct version of the application is deployed?**

Write your answer here.

Confirming that the correct version of our React application is successfully deployed to the production Nginx server requires verifying both the physical file metadata on the host server and the application build stamps inside the compiled code.
also Checking File Timestamps and Permissions
---

# Task 6 — Nginx Configuration Failure Simulation

## Goal

Simulate a real-world Nginx misconfiguration and recover the service safely.

### Evidence

#### Screenshot 1 — Output of `sudo nginx -t` showing the syntax error (broken config)

Add your screenshot here.

---

#### Screenshot 2 — Output of `sudo nginx -t` showing syntax ok (fixed config)

Add your screenshot here.

---

#### Screenshot 3 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What caused the configuration failure?**

Write your answer here.

The configuration failure was caused by a syntax error: the deletion of the mandatory terminating semicolon (;) from the try_files directive line.

When Nginx parsed the file during the syntax check (sudo nginx -t), it failed because it could no longer identify where the try_files instruction ended.
---

**2. How did you fix the issue?**

Write your answer here.

---

**3. How can you avoid this kind of issue in real production systems?**

Write your answer here.

The configuration failure was resolved by restoring the missing semicolon (;) at the end of the try_files directive line inside the /etc/nginx/sites-available/default virtual host configuration file, followed by a safe syntax validation and a zero-downtime service reload.
---

# Task 7 — Web Application Failure Simulation

## Goal

Simulate missing deployment content and recover the application safely.

### Evidence

#### Screenshot 1 — Output of `curl -I http://<public-ip>` showing failure (non-200 response)

Add your screenshot here.

---

#### Screenshot 2 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What caused the application to break in this scenario?**

Write your answer here

The application broke because of a Missing Static Asset Dependency.

Specifically, Nginx could no longer resolve its configured Document Root (root /var/www/html;). By moving the contents of /var/www/html to /var/www/html_backup, the target directory was left completely empty. As a result, the server could not find the physical index.html file required to initialize the React application.
---

**2. How did you fix the issue and restore the application?**

Write your answer here.

The application was restored by executing a Disaster Recovery Rollback. This involved deleting the empty placeholder directory, restoring the archived original build files from the backup directory back to /var/www/html, and restarting Nginx to clear system file descriptor caches.
---

**3. What steps would you take to prevent this kind of issue in real production systems?**

Write your answer here.

In a professional production environment, manually running commands on a live virtual machine (like using nano or executing raw mv and rm commands) is highly discouraged. It introduces significant human error, configurations drift over time, and a single typo can lead to costly downtime
---

# Task 8 — Security & Reliability Review

## Goal

Review and reflect on the security and reliability practices applied during this assignment.

### Security & Reliability Notes

Answer the following in your own words:

**1. Why is SSH key-based authentication more secure than sharing passwords?**

Write your answer here.

SSH key-based authentication is fundamentally more secure because it replaces a shared, guessable secret (a password) with asymmetric cryptography. This mathematical design eliminates the threat of brute-force attacks, prevents credentials from ever being sent across the network, and ensures that compromise of the server does not expose the client's private secret.
---

**2. Why should only required ports be open on a production server?**

Write your answer here.

Limiting open ports is the fundamental implementation of the Principle of Least Privilege at the network level. Opening only required ports minimizes the server's attack surface, ensuring that malicious actors cannot discover, interact with, or exploit backend services that were never meant to be publicly accessible.
---

**3. Why is it important for Nginx to be enabled on boot?**

Write your answer here.

Enabling Nginx on boot is critical for achieving high availability (HA) and automated disaster recovery. It ensures that if the underlying virtual machine restarts—due to an unexpected crash, hardware failure, cloud hypervisor maintenance, or a planned system update—the web server automatically starts serving traffic again without requiring manual human intervention.
---

**4. What are the risks of sharing secrets, keys, or credentials publicly?**

Write your answer here.

Sharing secrets, keys, or credentials publicly (such as committing them to a public GitHub repository) represents one of the most critical security failures in modern cloud computing.

Because modern cloud environments are fully automated, malicious actors do not search for keys manually. Instead, they run automated scraping bots that constantly scan public code repositories, forums, and paste sites. A leaked API key or SSH key is typically discovered and exploited by a bot within three seconds of being pushed online.
---

**5. Why should cloud resources be stopped or terminated when they are no longer needed?**

Write your answer here.

To prevent engineers from forgetting to clean up, modern DevOps organizations implement automation:

Auto Scaling Groups (ASGs): Dynamically shrink the number of active servers down to a minimum at night when traffic is low, and scale up during the day.

Scheduled Shutdown Scripts: Auto-stop non-production/development servers automatically outside of working hours (e.g., stopping all dev EC2 instances at 7:00 PM every weekday).

Infrastructure as Code (IaC) Teardown: In CI/CD pipelines, environments are spun up using tools like Terraform or CloudFormation to run automated tests, and are immediately destroyed (terraform destroy) the minute the tests pass.
---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`__________________________`

---

#### Screenshot — Published LinkedIn post

Add your screenshot here.

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [ ] Task 1: Screenshots (browser, ip a, ss -tulpen, ufw status) + Notes answered
- [ ] Task 2: Screenshots (nginx status, nginx -t, ss port 80) + Notes answered
- [ ] Task 3: Screenshots (access log, error log, journalctl) + Notes answered
- [ ] Task 4: Screenshots (uptime, free -h, df -h, du -sh) + Notes answered
- [ ] Task 5: Screenshots (ls html, grep deployed by, grep try_files) + Notes answered
- [ ] Task 6: Screenshots (nginx -t fail, nginx -t pass, curl recovery) + Notes answered
- [ ] Task 7: Screenshots (curl failure, curl recovery) + Notes answered
- [ ] Task 8: Security & Reliability Notes answered
- [ ] LinkedIn post published and URL submitted
- [ ] Full Name visible in all required screenshots
- [ ] No sensitive data exposed

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