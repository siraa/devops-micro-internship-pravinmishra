# Assignment 6 — Kubernetes Health Probes (Liveness)

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will configure and test a Kubernetes liveness probe. You will create a baseline NGINX Deployment, add an HTTP liveness probe, intentionally make it fail, observe container restarts, and restore the working configuration.

---

# Task 0 — Prepare the Lab Environment

## Goal

Confirm that you are connected to your learning cluster and remove the previous HPA and NGINX Deployment before starting this lab.

No screenshot or written notes are required for this pre-check.

---

# Task 1 — Set Up Your Lab Directory

## Goal

Create and enter a dedicated directory for the liveness-probe lab.

### Evidence

#### Screenshot 1 — Terminal showing that you are working inside `~/k8s-labs/health-probes-liveness`

![alt text](screenshots\sc1-T1-ass6-week12.png)

---

### Notes

**1. What problem does a liveness probe solve?**

A liveness probe detects when a container is unhealthy or stuck. If the probe fails repeatedly, Kubernetes automatically restarts the container to restore its normal operation.

**2. What is the key difference between a readiness probe and a liveness probe?**

A readiness probe checks whether a Pod is ready to receive traffic. If it fails, the Pod is removed from service endpoints but is not restarted. A liveness probe checks whether a container is still healthy. If it fails repeatedly, Kubernetes restarts the container.

---

# Task 2 — Create a Baseline Deployment Without Probes

## Goal

Create a baseline NGINX Deployment with two replicas and no health probes.

### Evidence

#### Screenshot 2 — Completed `00-nginx-deploy-baseline.yaml` manifest

![alt text](screenshots\sc16-T5-ass5-week12.png)

---

#### Screenshot 3 — Output showing a successful rollout and two running NGINX Pods

![alt text](screenshots\sc3-T2-ass6-week12.png)

---

#### Screenshot 4 — Relevant output from `kubectl describe deployment nginx-deployment`

![alt text](screenshots\sc4-T2-ass6-week12.png)

---

### Notes

**1. How many replicas does this Deployment maintain?**

The Deployment maintains 2 replicas (Pods).

**2. Are any health probes configured in the baseline Deployment?**

No, the baseline Deployment does not have any health probes configured.

**3. Why is a baseline useful before adding a liveness probe?**

A baseline provides a reference point to compare the Deployment's behavior before and after adding a liveness probe. It helps identify the impact of the probe on container health and automatic restarts.

---

# Task 3 — Add a Liveness Probe

## Goal

Add an HTTP liveness probe so Kubernetes can periodically check whether the NGINX container is still responsive.

### Evidence

#### Screenshot 5 — Completed `01-nginx-deploy-liveness.yaml` manifest showing the liveness probe

![alt text](screenshots\sc5-T2-ass6-week12.png)

---

#### Screenshot 6 — Output showing successful rollout and running Pods


![alt text](screenshots\sc6-T2-ass6-week12.png)
---

#### Screenshot 7 — Pod description showing liveness-probe details

![alt text](screenshots\sc6-T2-ass6-week12.png)

---

### Notes

**1. What path and port does the liveness probe check?**

The liveness probe checks the HTTP path / on port 80.

**2. What does `initialDelaySeconds: 10` do?**

It tells Kubernetes to wait 10 seconds after the container starts before performing the first liveness check.

**3. What does `failureThreshold: 3` mean?**

It means Kubernetes restarts the container after 3 consecutive failed liveness probes.

**4. Does a successful liveness probe control whether a Pod receives traffic? Explain briefly.**

Add No. A liveness probe only checks whether the container is healthy and needs restarting. A readiness probe controls whether a Pod is ready to receive traffic.

---

# Task 4 — Trigger and Observe a Liveness Restart

## Goal

Intentionally make the liveness probe fail and observe Kubernetes restart the unhealthy container.

### Evidence

#### Screenshot 8 — `02-nginx-liveness-broken.yaml` showing `/does-not-exist`

![alt text](screenshots\sc8-T4-ass6-week12.png)

---

#### Screenshot 9 — Pod output showing an increased RESTARTS count

![alt text](screenshots\sc9-T4-ass6-week12.png)

---

#### Screenshot 10 — Pod description showing liveness-probe failure events or restart details

![alt text](screenshots\sc10-T4-ass6-week12.png)

---

#### Screenshot 11 — Output showing stable running Pods after applying the fixed configuration

![alt text](screenshots\sc11-T4-ass6-week12.png)

---

### Notes

**1. Why did the liveness probe fail?**

The liveness probe failed because it checked the invalid HTTP path /does-not-exist, which returned an HTTP 404 error.

**2. What happened after Kubernetes detected repeated liveness-probe failures?**

After 3 consecutive failures, Kubernetes restarted the container. Repeated failures caused the Pods to enter CrashLoopBackOff.

**3. What does the RESTARTS column prove?**

The RESTARTS column shows how many times a container has restarted. An increased number confirms that Kubernetes restarted the container after liveness-probe failures.

**4. How did you restore the Deployment to a stable state?**

I restored the correct liveness-probe configuration by applying 03-nginx-liveness-fixed.yaml, which changed the path back to /. The Deployment rolled out successfully, and both Pods returned to 1/1 Running with 0 restarts.

---

# Task 5 — Optional: Observe a Restart Loop

## Goal

Observe why overly aggressive or incorrectly configured liveness probes can create restart loops.

Complete this section only if you attempt Task 5. Otherwise, mark it as “Not attempted.”

**Task status:** Completed / Not attempted

### Evidence

#### Optional Screenshot — Pod output showing the RESTARTS count increasing during the restart-loop observation

![alt text](screenshots\sc11-T4-ass6-week12.png)

![alt text](screenshots\final-deployment-ass6-week12.png)

No specific restart count or CrashLoopBackOff status is required.

---

### Notes — Answer Only If Attempted

**1. What is a restart loop?**

A restart loop occurs when a container repeatedly fails its health checks and Kubernetes keeps restarting it. If the problem persists, the Pod enters the CrashLoopBackOff state, where Kubernetes gradually increases the delay between restart attempts.

**2. Why can an aggressive liveness probe be harmful?**

An aggressive liveness probe can restart a healthy container because it does not allow enough time for the application to start or recover from temporary issues. This can cause unnecessary restarts, service interruptions, and reduced application availability.

**3. Why should liveness checks be lightweight and carefully tuned?**

Liveness checks should be lightweight to avoid consuming too many resources or affecting application performance. They should also be carefully tuned to detect real failures without triggering unnecessary restarts due to temporary delays or slow responses.

---

# Task 6 — Share Your Liveness-Probe Learning on LinkedIn

## Goal

Share what you learned about Kubernetes liveness probes, container restarts, and the difference between readiness and liveness.

### Evidence

#### Screenshot 12 — Published LinkedIn post

![alt text](screenshots\post-linked-ass6-week12.png)

#### Linkedin post URL

https://lnkd.in/p/d-xpHQaZ

---

# Submission Instructions

- Add all required screenshots numbered 1–12.
- Include the optional Task 5 screenshot only if you attempt that task.
- Ensure your full name is visible in required screenshots.
- Use clear, readable screenshots showing the relevant manifest or output.
- Include the completed baseline and liveness-probe manifests in your repository.
- Answer all Notes questions for Tasks 1–4 in your own words.
- If you attempt Task 5, complete its Notes and restore the working configuration afterward.
- Do not expose tokens, passwords, kubeconfig files, account IDs, or other sensitive information.

---

# Completion Checklist

- [ ] Learning cluster confirmed and previous assignment resources removed where applicable
- [ ] Liveness-probe lab directory created (Screenshot 1)
- [ ] Baseline Deployment manifest completed (Screenshot 2)
- [ ] Successful rollout and two baseline Pods verified (Screenshot 3)
- [ ] Baseline Deployment details reviewed (Screenshot 4)
- [ ] Liveness probe added successfully (Screenshot 5)
- [ ] Successful liveness-probe rollout verified (Screenshot 6)
- [ ] Liveness-probe details inspected (Screenshot 7)
- [ ] Broken liveness configuration created (Screenshot 8)
- [ ] Increased container restart count observed (Screenshot 9)
- [ ] Liveness-probe failure events or restart details captured (Screenshot 10)
- [ ] Working liveness configuration restored and stable Pods verified (Screenshot 11)
- [ ] Optional Task 5 evidence and Notes completed, if attempted
- [ ] Working configuration restored after Task 5, if attempted
- [ ] LinkedIn post published (Screenshot 12)
- [ ] Notes for Tasks 1–4 completed
- [ ] Required manifests included
- [ ] All required screenshots are clear and readable
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

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*
