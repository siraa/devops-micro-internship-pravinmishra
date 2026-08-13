# Assignment 4 — Gotto Job: Backlog Refinement & Sprint 1 in Jira

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this 90-minute, time-boxed exercise, you will act as a Scrum team — or run in Solo Mode, playing every role yourself — to turn the Gotto Job template into a value-ordered backlog, estimate the work in story points, plan Sprint 1, open the burndown chart, and ship one small UI-only increment (text, color, spacing, a label, or a CTA — no backend changes).

---

# Task 1 — Roles & Mode Setup (Team vs Solo)

## Goal

Choose Team Mode or Solo Mode, and document how each Scrum role (Product Owner, Scrum Master, Dev Lead, DevOps Lead) was handled.

### Evidence

#### Screenshot 1 — Jira "Create project" screen, or the project sidebar after creation

![alt text](screenshots\sc1-T1-ass4-week5.png)

---

### Notes

Write one line for each role: PO (what you prioritized), SM (how you ensured process), Dev Lead (what you built), DevOps Lead (how you shipped).

Product Owner: Prioritized high-impact user stories to deliver a fully functional portfolio increment and footer within the 5-day mini-sprint timeframe.

Scrum Master: Managed daily standups, resolved blockers, and monitored the Burndown Chart to maintain steady velocity and process alignment.

Dev Lead: Built the frontend template and implemented the automated JavaScript logic for dynamic deployment-date formatting.

DevOps Lead: Provisioned the AWS EC2 instance, configured Nginx, and securely transferred production build assets via SSH/SCP.

---

# Task 2 — Create the Jira Project (Team-managed → Scrum)

## Goal

Create a Team-managed Scrum project named `Gotto Job – Team <#>` (Team Mode) or `Gotto Job – <YourName>` (Solo Mode).

### Evidence

#### Screenshot 2 — Project created page showing the project name and key

![alt text](screenshots\sc1-T1-ass4-week5.png)

---

# Task 3 — Create the Epic

## Goal

Create the Epic `Improve Gotto Job UI discoverability & trust` to group the UI improvement initiative.

### Evidence

#### Screenshot 3 — Backlog showing the Epic panel with the Epic visible

![alt text](screenshots\sc1-T1-ass4-week5.png)

---

# Task 4 — Seed the Product Backlog (6–8 Stories + Fibonacci Points + Ranking)

## Goal

Create at least six Stories under the Epic, estimate each with 1, 2, or 3 story points, and rank them by value.

### Evidence

#### Screenshot 4 — Backlog showing the Epic and at least six Stories under it

![alt text](screenshots\sc4-T4-ass4-week5.png)

---

#### Screenshot 5 — One Story opened showing its Story Points and acceptance criteria filled in

![alt text](screenshots\sc5-T4-ass4-week5.png)

---

# Task 5 — Planning Poker (Estimate + Debate Notes)

## Goal

Confirm the Story Points (1, 2, or 3) for each Story and record brief reasoning for each estimate.

### Evidence

#### Screenshot 6 — Backlog showing Story Points visible, or two or three Stories opened showing their points

![alt text](screenshots\sc6-T5-ass4-week4.png)

---

### Notes

For each story, explain in one or two lines why it is a 1, 2, or 3 (mention any debate, even in Solo Mode).

S1 – Hero tagline (1 point): This only requires changing one heading, so it is a small task.
S2 – Button colour (1 point): Only the button colour needs to be updated. I briefly considered 2 points because it affects multiple buttons, but I kept it at 1 since it is a simple CSS change.
S3 – Job card typography (2 points): This requires changing the font size and weight, then checking that the layout still looks correct on different screen sizes.
S4 – REMOTE badge (2 points): This involves adding a new badge and displaying it only for remote jobs, making it slightly more complex than a simple text change.
S5 – Posted on date (1 point): This is a simple text addition with no extra logic.
S6 – Search labels (2 points): Several labels and placeholders need to be updated and tested, so it requires more work than a single text change.In your submission, write one or two sentences explaining why each Story received its Story Point estimate.
S7 – Job Detail "Apply Now" Button (1 Point)
Adds a single "Apply Now" button that links to an email address or placeholder link. It is a simple change with no additional logic, so it is estimated as 1 point.
S8 – Footer Trust Links (1 Point)
Adds two footer links ("About" and "Contact"). This only requires a small HTML update with no complex functionality, so it is estimated as 1 point.

Total backlog points across all 8: 1+1+2+2+1+2+1+1 = 11 points

---

# Task 6 — Sprint Planning: Create Sprint 1 + Sprint Goal + Scope

## Goal

Create Sprint 1, move three or four Stories into it (approximately 3–6 points), set the Sprint Goal, and break each selected Story into Build, Verify, Deploy, and Screenshot Sub-tasks.

### Evidence

#### Screenshot 7 — Sprint 1 with the selected Stories inside it

![alt text](screenshots\sc7-T6-ass4-week5.png)

---

#### Screenshot 8 — One Story showing the Sub-tasks created

![alt text](screenshots\sc8-T6-ass4-week5.png)

---

# Task 7 — Reports: Open Burndown Chart

## Goal

Open the Burndown Chart and confirm it exists for Sprint 1. It is acceptable if the chart is not yet populated.

### Evidence

#### Screenshot 9 — Burndown Chart page opened, even if empty

![alt text](screenshots\sc9-T7-ass4-week5.png)

---

# Task 8 — Ship One Small Increment (Build + Deploy + Proof)

## Goal

Implement one small UI-only Story from Sprint 1, commit it, deploy it live, and move the Story and its Sub-tasks to Done in Jira.

### Evidence

#### Screenshot 10 — Jira board showing the Story moved to Done

![alt text](screenshots\sc10-T8-ass4-week5.png)

---

#### Screenshot 11 — Git commit output

![alt text](screenshots\commit-gottojob-ass4-week5.png)

---

#### Screenshot 12 — Live URL in the browser showing the UI change, with the URL visible

![alt text](screenshots\site-find-your-ass4-week5.png)

---

# Task 9 — Retro Notes (Scrum Pillar + Value)

## Goal

Add a retro comment covering what went well, what to improve, one Scrum pillar observed (Transparency, Inspection, or Adaptation), and one Scrum value (Openness, Focus, Commitment, Courage, or Respect).

### Evidence

#### Screenshot 13 — Jira retro comment visible

![alt text](screenshots\sc13-T9-ass4-week5.png)

---

# Task 10 — LinkedIn Post (Mandatory)

## Goal

Publish a LinkedIn post about what you delivered, including your live URL, three to five lines on what you did and learned, and one screenshot (Burndown Chart, Sprint board, or the live UI change).

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/taysir-ouaslati-9b6527a3_agile-estimation-backlog-refinement-breaking-share-7492248708432957440-QRnG/?utm_source=share&utm_medium=member_desktop&rcm=ACoAABX4AtoB0tpceeC8Jnqozhzdi2ViZ02bFHk

---

#### Screenshot 14 — Published LinkedIn post

![alt text](screenshots\linked-in-ass4-week5.png)

---

# Submission Instructions

- Add all 14 required screenshots
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [ ] Task 1: Team Mode or Solo Mode selected and all four roles documented (Screenshot 1 & Notes)
- [ ] Task 2: Team-managed Scrum project created with the required name (Screenshot 2)
- [ ] Task 3: UI improvement Epic created (Screenshot 3)
- [ ] Task 4: 6–8 Stories added under the Epic and ranked by value (Screenshots 4 & 5)
- [ ] Task 5: Story Points set (1, 2, or 3) with reasoning recorded (Screenshot 6 & Notes)
- [ ] Task 6: Sprint 1 created with Sprint Goal, 3–4 Stories, and Sub-tasks (Screenshots 7 & 8)
- [ ] Task 7: Burndown Chart opened (Screenshot 9)
- [ ] Task 8: One UI-only increment implemented, committed, deployed, and verified (Screenshots 10–12)
- [ ] Task 9: Retro comment with one Scrum pillar and one Scrum value (Screenshot 13)
- [ ] Task 10: Mandatory LinkedIn post published with the live URL, backlog refinement, Sprint planning, one shipped increment, proof, and Screenshot 14
- [ ] Full Name visible in required screenshots
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
