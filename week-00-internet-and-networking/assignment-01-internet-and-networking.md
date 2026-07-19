# Week 00 - Internet and Networking

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

# 🧑‍💻 Task 1: Using ChatGPT as Your Learning Assistant

## Scenario

You're new to DevOps and will frequently encounter technical questions. ChatGPT can be your learning companion.

## Your Task

Write a clear ChatGPT prompt to help you understand:

> "What is a protocol in networking? Explain with a simple real-life example."

Take a screenshot of your interaction showing:

* Your detailed prompt (with clear expectations)
* ChatGPT's simplified response with an example

## Screenshot

Save your screenshot in the `screenshots` folder and update the file name below.

![Task 1 Screenshot](screenshots/task-1-chatgpt.png)


Replace `task-1-chatgpt.png` with your actual screenshot file name.

---

## What I Learned (2–3 lines)

I learned that a network protocol is essentially a strict set of rules and formats that enables different devices to communicate clearly without misunderstanding. By prompting the AI to use a real-life analogy, I saw how complex data exchange rules mirror everyday human interactions, like two people agreeing to speak the same language and taking turns to talk.

---

# 🌐 Task 2: Internet and Networking

## Scenario

Your friend is launching an online bookstore named **EpicReads**.

He asked you to explain how users globally can access his website hosted in Finland.

## Your Task

Write a short explanation (**100–150 words**) that includes:

* Packet Switching
* IP Address
* TCP/IP
* HTTP/HTTPS

💡 **Tip:** You may use ChatGPT (as demonstrated in Task 1) to refine your explanation.

## Answer
The internet relies on a structured network hierarchy to transmit data seamlessly. At the core is packet switching, a method where data is broken into small chunks called packets, sent independently across optimal network paths, and reassembled at the destination.

To route these packets accurately, every connected device is assigned a unique IP address, which acts like a digital mailing address. This entire process is governed by TCP/IP (Transmission Control Protocol/Internet Protocol). TCP guarantees that all packets arrive safely and in the correct order, while IP handles the actual routing to the target address.

Finally, application-layer protocols utilize this established network pipeline to exchange data. HTTP (Hypertext Transfer Protocol) is the standard foundation for data communication on the web, while HTTPS adds a vital layer of SSL/TLS encryption, securing the data transmission from potential interceptors.

---

# 🏗️ Task 3: Application Architecture & Stack

## Scenario

EpicReads bookstore has two application versions:

### Two-Tier Application

* Frontend
* Database

### Three-Tier Application

* Frontend
* Backend
* Database

## Your Task

* Draw simple diagrams (hand-drawn or tool-based such as draw.io)
* Label each layer clearly
* List at least two common technologies or tools used for each layer
* Submit a screenshot or photo clearly showing your own drawing

## Diagram Screenshot / Photo

Save your diagram image in the `screenshots` folder and update the file name below.

![Application Architecture Diagram](screenshots/task-3-diagram.png)


Replace `task-3-diagram.png` with your actual diagram file name.

---

## Technologies Used

### Frontend

 * React: A popular, component-based JavaScript library utilized to build the core responsive UI and dynamic views of the EpicReads app.

* HTML5 & CSS3 / Tailwind CSS: Used for structuring the website framework and crafting the modern design layouts and typography.


### Backend

* Node.js: A lightweight, event-driven JavaScript runtime environment widely implemented to handle asynchronous API requests and server-side execution logs.

* Express.js: A minimal and flexible Node.js web application framework used to construct robust RESTful API endpoints for managing bookstore operations.

### Database

* MongoDB / PostgreSQL: The primary database layer utilized for storing persistent bookstore assets, user credentials, and book inventories.

* Mongoose / Prisma: An Object Data Modeling (ODM) or Object-Relational Mapping (ORM) tool used to safely manage schemas and query data cleanly from the backend application logic.

---

# 🌍 Task 4: Domain Name & DNS (Basic Concepts)

## Scenario

Your friend's bookstore **EpicReads** is currently accessible through:

```text
52.172.142.222:3000
```

He purchased the domain:

```text
epicreads.com
```

## Your Task

In **50–100 words**, explain in your own words:

1. What is DNS (Domain Name System)?
2. Which DNS record type should be used to connect the domain to the given IP, and why?

## Answer

1. The Domain Name System (DNS) is the phonebook of the internet. Humans access information online through easy-to-remember domain names like example.com. Web browsers, however, interact through Internet Protocol (IP) addresses (such as 192.0.2.1 or 2001:db8::1).

DNS translates these human-readable domain names into machine-readable IP addresses so that browsers can load the requested internet resources. Without DNS, you would have to remember a complex string of numbers for every website you wanted to visit.

2. To connect a domain name directly to a standard IPv4 address, you must use an A Record (Address Record).

---

# 💻 Task 5: Visual Studio Code Setup (Hands-on)

## Your Task

Install Visual Studio Code (if not already installed).

Take a screenshot of your VS Code environment showing:

* Terminal open inside VS Code
* Running a basic command:

### Windows

```powershell
dir
```

### Linux / macOS

```bash
pwd
ls
```

* Your selected VS Code theme clearly visible

⚠️ **Important:** The screenshot must show your username or another identifiable detail to confirm it is your environment.

## Screenshot

Save your screenshot in the `screenshots` folder and update the file name below.

![VS Code Setup Screenshot](screenshots/task-5-vscode.png)


Replace `task-5-vscode.png` with your actual screenshot file name.

---

# 🔗 Task 6: Publish Your Assignment as a LinkedIn Post

## Objective

Publishing on LinkedIn helps you:

* Build your professional online presence
* Reinforce your learning
* Document your DevOps journey publicly

## Your Task

Summarize your answers from Tasks 1–5 into a LinkedIn post.

Clearly structure your post into the following sections:

* ChatGPT
* Internet & Networking
* App Architecture
* DNS
* VS Code Setup

Add the following credit note at the end of your post:

> **P.S. This post is part of the DevOps Micro Internship (DMI) with Agentic AI — Cohort 3 — by Pravin Mishra. My graded progress is public: https://dmi.pravinmishra.com/s/YOUR-GITHUB-USERNAME.html · Start your DevOps journey: https://dmi.pravinmishra.com/?utm_source=student&utm_medium=ps-linkedin&utm_campaign=cohort3**

---

## LinkedIn Post URL

https://www.linkedin.com/posts/taysir-ouaslati-9b6527a3_i-recently-completed-the-first-5-tasks-of-activity-7461310573104803840-q2WB?utm_source=share&utm_medium=member_desktop&rcm=ACoAABX4AtoB0tpceeC8Jnqozhzdi2ViZ02bFHk

---

## LinkedIn Post Backup Copy

I recently completed the first 5 tasks of the DevOps Micro Internship, and I want to share what I picked up. This is my public accountability post!

ChatGPT as a Learning Assistant

One of the first things we were taught: use AI as a learning partner, not a shortcut. Asking better questions leads to better understanding. Instead of "explain DevOps," I learned to ask "explain DevOps to me like I'm a designer trying to transition into tech." The specificity changes everything

Internet & Networking
Ever wondered how a website hosted in Finland loads on your screen in Lagos in under a second? Here's the short version:
Every device has an IP address that identifies it on the network. Data travels using the TCP/IP model, broken into small packets via packet switching, each finding its own route and reassembling on arrival. The browser communicates with the server using HTTP or HTTPS, with HTTPS adding a layer of encryption to keep your data safe in transit.
App Architecture
This task introduced me to how modern applications are structured, the layers involved (frontend, backend, database), and how they talk to each other. Understanding architecture is foundational before you can automate or deploy anything.
DNS — The Internet's Phonebook
DNS (Domain Name System) converts human-readable domain names like epicreads.com into IP addresses like 52.172.142.222 that computers actually use to find servers.
To point a domain to a server, you use an A Record, which maps the domain name directly to an IPv4 address. Without DNS, we'd all be memorizing long strings of numbers just to visit websites.
VS Code Setup
Got my local development environment up and running! VS Code is the industry standard for a reason. Extensions, terminal integration, and Git support all in one place. Small setup, big impact.
These 5 tasks gave me a real foundation for everything that comes next in DevOps. If you're a creative professional curious about the technical side of how the internet works, I'd genuinely recommend starting here.


P.S. This post is part of the FREE DevOps Micro Internship Cohort run by Pravin Mishra 

---

# Reflection – Week 0

### What did you find easy?

React Build Generation: Compiling the production assets using standard build tools was straightforward and ran without any dependency issues.

Basic Nginx Installation: Installing Nginx on the Ubuntu virtual machine and verifying that the daemon was active via systemctl was highly accessible.

Writing Diagnostic Automation: Developing the foundational, read-only Bash telemetry script to safely gather server parameters (like port maps and configuration tests) was easy to implement.

---

### What was difficult?

Nginx Path Misconfigurations: Debugging the exact absolute path alignment inside the Nginx server block was tricky, specifically correcting the root directive so it pointed away from default directories and directly to the localized application build path.

Handling SPA Client-Side Routing: Troubleshooting the "404 Not Found" errors that occur when refreshing pages on a deployed React application required understanding and applying the precise Nginx try_files fallback routing rules.

Security & Firewall Alignment: Managing the balance between enclosing the network perimeter by closing non-essential ports via UFW while simultaneously maintaining stable, public HTTP traffic flow over Port 80.

---

### What will you improve next week?
Domain & SSL Automation: Map a custom DNS A record to the public IP address and automate full HTTPS encryption by setting up Let's Encrypt certificates using Certbot.

Continuous Integration Pipelines: Move away from manual build transfers by introducing automation scripts or simple CI/CD pipelines to deploy new code updates directly to the VM.

Expanded Telemetry Scripts: Enhance the read-only Bash triage script to monitor CPU, memory margins, and log traffic patterns over time, ensuring a deeper view of the app's health.

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.


## 📌 Resources

- 🌐 **DMI Official Website:** https://pravinmishra.com/dmi  
- 🎓 **DevOps for Beginners (Udemy):** https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 **Ultimate Agentic AI DevOps with Clude Code** https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/?referralCode=448389767BC96284087B
- 🎓 **DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm** https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/?referralCode=1C5B734505D65A010FA3
- ▶️ **YouTube Playlist (DMI Cohort 3):** https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 **Pravin Mishra (LinkedIn):** https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 **CloudAdvisory (LinkedIn):** https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track*