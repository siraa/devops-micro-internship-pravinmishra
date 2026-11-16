# Assignment 5 — Kubernetes Health Probes (Readiness)

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will configure and test a Kubernetes readiness probe. You will create a baseline NGINX Deployment, add an HTTP readiness probe, intentionally break it, observe a stalled rollout, and restore the working configuration.

You will also demonstrate how readiness probes protect rolling updates.

---

# Task 0 — Prepare the Lab Environment

## Goal

Confirm that you are connected to your learning cluster and remove the previous assignment’s HPA and NGINX Deployment before starting this lab.

No screenshot or written notes are required for this pre-check.

---

# Task 1 — Set Up Your Lab Directory

## Goal

Create and enter a dedicated directory for the readiness-probe lab.

### Evidence

#### Screenshot 1 — Terminal showing that you are working inside `~/k8s-labs/health-probes-readiness`

![alt text](screenshots\scx1-T1-ass5-week12.png)

---

### Notes

**1. What is the purpose of a readiness probe?**

A readiness probe checks whether a container is ready to receive network traffic. Kubernetes only sends traffic to a Pod when its readiness probe succeeds.

**2. Why is a Pod being Running not always enough to confirm it can serve traffic?**

A Pod can be Running while its application is still starting, initializing, or experiencing problems. Running only indicates that the container is running, not that it is ready to handle requests.

---

# Task 2 — Create a Baseline Deployment Without Probes

## Goal

Create a baseline NGINX Deployment with two replicas and no readiness probe.

### Evidence

#### Screenshot 2 — Completed `00-nginx-deploy-baseline.yaml` manifest

![alt text](screenshots\scx1-T1-ass5-week12.png)

---

#### Screenshot 3 — Output showing a successful rollout and two running NGINX Pods

![alt text](screenshots\sc3-T2-ass5-week12.png)

---

#### Screenshot 4 — Relevant output from `kubectl describe deployment nginx-deployment`

![alt text](screenshots\describe-ass5-week12.png)

---

### Notes

**1. How many replicas does this Deployment maintain?**

The Deployment maintains 2 replicas.

**2. What does `maxUnavailable: 0` mean during a rolling update?**

It means that Kubernetes must keep all available replicas running during the rolling update. No existing healthy Pod should become unavailable before a replacement Pod is ready.

**3. Is a readiness probe configured in this baseline Deployment?**

No, the baseline Deployment does not have a readiness probe configured.

---

# Task 3 — Add a Readiness Probe

## Goal

Add an HTTP readiness probe so Kubernetes checks whether each NGINX container is ready to accept requests.

### Evidence

#### Screenshot 5 — Completed `01-nginx-deploy-readiness.yaml` manifest showing the readiness probe

![alt text](screenshots\sc5-T3-ass5-week12.png)

---

#### Screenshot 6 — Output of the successful rollout and ready Pods

![alt text](screenshots\sc3-T2-ass5-week12.png)



#### Screenshot 7 — Pod description showing readiness conditions and readiness probe details

![alt text](screenshots\describe-ass5-week12.png)
---

### Notes

**1. What path and port does the readiness probe check?**

The readiness probe checks the HTTP path / on port 80.

**2. What does `initialDelaySeconds: 2` do?**

It tells Kubernetes to wait 2 seconds after the container starts before performing the first readiness check.

**3. What does `periodSeconds: 5` do?**

It tells Kubernetes to perform a readiness check every 5 seconds.

**4. What must happen before Kubernetes considers this Pod ready?**

The readiness probe must succeed by receiving a successful HTTP response from the application at the configured path and port.

---

# Task 4 — Break and Restore Readiness

## Goal

Intentionally fail the readiness probe, observe the NotReady behavior, and then restore the working probe configuration.

### Evidence

#### Screenshot 8 — `02-nginx-readiness-broken.yaml` showing `/does-not-exist`

![alt text](screenshots\sc8-T4-ass5-week12.png)



#### Screenshot 9 — Output showing the rollout timeout or incomplete rollout

![alt text](screenshots\sc9-T4-ass5-week12.png)

---

#### Screenshot 10 — Pod details showing `Ready: False` or readiness-probe failure events

![alt text](screenshots\sc10-T4-ass5-week12.png)

---

#### Screenshot 11 — Output showing successful rollout and ready Pods after the fix

![alt text](screenshots\sc11-T4-ass5-week12.png)

---

### Notes

**1. Why did the readiness probe fail?**

The readiness probe failed because the configured path /does-not-exist did not exist on the NGINX server. NGINX returned HTTP status code 404.

**2. What happened to the Pod readiness status?**

The Pod remained in the Running state but its readiness status became False (0/1). This means the container was running but was not ready to receive traffic.

**3. Why did the rollout fail to complete?**

The rollout failed to complete because the new Pods could not pass their readiness probes. Kubernetes waited for them to become Ready before completing the rolling update. The maxUnavailable: 0 setting kept the old healthy Pods available.

**4. How did you restore the Deployment successfully?**

I restored the correct readiness probe path from /does-not-exist to / using the fixed YAML manifest. I applied the configuration with kubectl apply, and the rollout completed successfully with both Pods returning to Ready (1/1).

---

# Task 5 — See How Readiness Protects Rolling Updates

## Goal

Observe how readiness prevents a bad new Pod from replacing healthy Pods during a rolling update.

### Evidence

#### Screenshot 12 — Successful update command and rollout status

![alt text](screenshots\sc12-T5-ass5-week12.png)

---

#### Screenshot 13 — ReplicaSets and Pods after the successful rolling update

![alt text](screenshots\sc13-T5-ass5-week12.png)

---

#### Screenshot 14 — Completed `04-readiness-broken-patch.yaml`

![alt text](screenshots\sc14-T5-ass5-week12.png)

---

#### Screenshot 15 — Output showing the expected stalled rollout or timeout

![alt text](screenshots\sc15-T5-ass5-week12.png)

---

#### Screenshot 16 — Deployment or Pod output showing the effect of the broken readiness probe

![alt text](screenshots\sc16-T5-ass5-week12.png)

---

#### Screenshot 17 — Successful rollout status after restoring the fixed configuration

![alt text](screenshots\sc17-T5-ass5-week12.png)

---

### Notes

**1. What happened during the successful rolling update?**

Kubernetes gradually replaced the old Pods with new Pods using the updated NGINX image (nginx:1.23.1). The rollout completed successfully because the new Pods passed their readiness probes.

**2. Why did Kubernetes create a new ReplicaSet?**

Kubernetes created a new ReplicaSet because the Deployment's Pod template changed when the container image was updated. Each new Pod template revision is managed by a new ReplicaSet

**3. Why were the new Pods unable to replace the existing healthy Pods after readiness was broken?**

The new Pods failed their readiness probes because the configured path /does-not-exist returned HTTP 404. Kubernetes therefore marked them as NotReady and did not use them to replace the existing healthy Pods.

**4. How does `maxUnavailable: 0` protect availability during this rollout?**

It ensures that no existing available Pods are taken offline before replacement Pods become Ready. This helps maintain application availability throughout the rolling update.

---

# Task 6 — Share Your Readiness-Probe Learning on LinkedIn

## Goal

Share what you learned about Kubernetes readiness probes and how they protect rolling updates.

### Evidence

#### Screenshot 18 — Published LinkedIn post

![alt text](screenshots\sc18-T6-ass5-week12.png)

#### Linkedin post link

https://lnkd.in/p/duHcwxWY

---

# Submission Instructions

- Add all required screenshots numbered 1–18.
- Ensure your full name is visible in required screenshots.
- Use clear, readable screenshots showing the relevant manifest or output.
- Include the completed baseline and readiness-probe manifests in your repository.
- Answer all Notes questions for Tasks 1–5 in your own words.
- Include evidence of the intentional readiness failure and successful recovery.
- Do not expose tokens, passwords, kubeconfig files, account IDs, or other sensitive information.

---

# Completion Checklist

- [ ] Learning cluster confirmed and previous assignment resources removed where applicable
- [ ] Readiness-probe lab directory created (Screenshot 1)
- [ ] Baseline Deployment created successfully (Screenshot 2)
- [ ] Two baseline Pods running and successful rollout verified (Screenshot 3)
- [ ] Baseline Deployment details reviewed (Screenshot 4)
- [ ] Readiness probe added successfully (Screenshot 5)
- [ ] Successful readiness rollout verified (Screenshot 6)
- [ ] Pod readiness conditions and probe details inspected (Screenshot 7)
- [ ] Broken readiness configuration created (Screenshot 8)
- [ ] Stalled rollout or timeout observed (Screenshot 9)
- [ ] NotReady status or readiness-probe failure events captured (Screenshot 10)
- [ ] Working readiness configuration restored (Screenshot 11)
- [ ] Successful image update completed (Screenshot 12)
- [ ] ReplicaSets and Pods inspected after the update (Screenshot 13)
- [ ] Broken-readiness patch created (Screenshot 14)
- [ ] Stalled rollout observed after the patch (Screenshot 15)
- [ ] Effect of broken readiness captured (Screenshot 16)
- [ ] Final recovery completed (Screenshot 17)
- [ ] LinkedIn post published (Screenshot 18)
- [ ] Notes for Tasks 1–5 completed
- [ ] Required manifests included
- [ ] All screenshots are clear and readable
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
