# Assignment 5 — AI-Assisted Sprint Health Report via Jira MCP

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will connect Claude Code to your Jira board through an MCP server, the same way you connected it to GitHub in Week 2, and build a read-only `/sprint-health` skill. The skill reads your current sprint through Jira's API and reports sprint velocity, stories at risk of missing the sprint, and items missing an estimate — but it must never create, edit, comment on, or transition a single ticket itself. You will prove that boundary holds by making a real change on the board yourself and confirming the skill only ever reports, never acts.

---

# Task 1 — Create a Jira API Token

## Goal

Generate an API token from your Atlassian account that the MCP server will use to authenticate with your Jira site. Do not screenshot the token value itself.

### Evidence

#### Screenshot 1 — Jira API token creation confirmation page showing the token name, with the token value not visible

![alt text](screenshots\sc1-T1-ass5-week5.png)

### Notes You Must Write (Very Important):

Why does the MCP server need your site URL and account email in addition to the token?


When connecting an Model Context Protocol (MCP) server—especially for cloud-based tools, APIs, or platforms (like Jira, GitHub, or Shopify)—providing a site URL and account email alongside an API token is standard security architecture.

Here is why all three elements are required:

1. Site URL (The Target Endpoint)
Identifies your specific instance: Many cloud services (like Atlassian/Jira or enterprise platforms) do not run on a single universal endpoint. Your site URL (e.g., [https://your-company.atlassian.net](https://your-company.atlassian.net)) tells the MCP server where to direct its API requests.

Routes traffic correctly: Without the URL, the token is just a key without a door—the system wouldn't know which tenant or workspace server to connect to.

2. Account Email (The User Identity)
Provides context for API Authentication: Many APIs (such as Atlassian's REST API) use Basic Authentication via API Tokens. In this scheme, the API expects a combination of Username/Email + API Token to authenticate requests.

Audit Logging & Permissions: The server needs to execute actions on behalf of a specific user. The email identifies who is performing the action (for instance, creating a ticket or updating a repository), ensuring that actions respect that specific user's permission levels.

3. API Token (The Secret Authorization)
Replaces your password: The token acts as a secure, revokable password generated specifically for third-party access.

Grants access: While the email says who you are and the URL says where to go, the token proves that you actually have permission to access that account.

Think of it like checking into a hotel:

Site URL: The hotel's address (where to go).

Account Email: Your name/ID (who you are).

API Token: Your keycard (proves you are authorized to enter).

---

# Task 2 — Create .mcp.json at the Project Root

## Goal

Create or update `.mcp.json` at your project root with a Jira MCP server block, following the same shape as the GitHub MCP server you configured in Week 2.

### Evidence

#### Screenshot 2 — `.mcp.json` open in VS Code showing the Jira server configuration

![alt text](screenshots\sc2-T2-ass5-week5.png)

### Notes You Must Write (Very Important):

Compare this jira block to the github block from Week 2 Assignment 5. The GitHub server ran via npx (a Node.js package); this one runs via uvx (a Python package) — what stays exactly the same shape despite that difference, and why doesn't Claude Code care which language a given MCP server is written in?

Compare this Jira block to the GitHub block from Week 2 Assignment 5. The GitHub server ran via npx (a Node.js package); this one runs via uvx (a Python package) — what stays exactly the same shape despite that difference, and why doesn't Claude Code care which language a given MCP server is written in?

1. What stays exactly the same in the .mcp.json file?
Despite switching from a Node.js command (npx) to a Python command (uvx), the overall structure of the JSON object does not change at all.

Here is what keeps the exact same shape:

The root mcpServers key: It is always the main object that groups the list of servers.

The server name: You define a custom identifier key (e.g., "jira" instead of "github").

The internal structure: The required attributes remain identical:

command: A string indicating the executable to run ("npx" or "uvx").

args: An array of strings ([...]) containing the package name and its parameters.

env: An object containing environment variables (API keys, tokens, URLs, etc.).

2. Why doesn't Claude Code care which language the MCP server is written in?
Claude Code does not care at all because the MCP (Model Context Protocol) relies on a standardized client-server architecture based on system streams (stdio).

---

# Task 3 — Add Your Credentials to settings.local.json

## Goal

Add your Jira site URL, account email, and API token to `.claude/settings.local.json`, and confirm that file is listed in `.gitignore` so it is never committed.

### Evidence

#### Screenshot 3 — `settings.local.json` open in VS Code showing the `env` section, with the actual token value blurred or covered

![alt text](screenshots\sc3-T3-ass5-week5.png)

### Notes You Must Write (Very Important):

Why must JIRA_API_TOKEN live in settings.local.json and never in .mcp.json?

1. Version Control & Git Leak Prevention
.mcp.json is tracked by Git: This file defines the server structure and configuration for the project. It is intended to be committed to your repository and shared with teammates or pushed to remote platforms like GitHub.

Hardcoding secrets = instant security breach: If you place an active API token inside .mcp.json, it will be saved in your Git history and exposed to anyone with access to the repo (or scraped by public GitHub bots within seconds).

2. Local-Only Storage (settings.local.json)
settings.local.json is ignored by Git: This file is specifically designed for environment-specific variables, paths, and credentials unique to your personal machine.

Ignored via .gitignore: Standard repository setups include *.local.json or settings.local.json in the .gitignore file, ensuring these sensitive credentials never leave your local workspace.

💡 Best Practice Pattern
Instead of hardcoding the secret:

.mcp.json (Shared Architecture): Defines how to run the MCP server and references environment variable keys.

settings.local.json (Local Secrets): Supplies the actual secret value ("JIRA_API_TOKEN": "ATATT3xF...") securely on your local environment.

---

# Task 4 — Verify the Connection with /mcp

## Goal

Restart Claude Code and confirm the Jira MCP server shows as connected.

### Evidence

#### Screenshot 4 — `/mcp` output showing `jira: connected`

![alt text](screenshots\sc4-T4-ass5-week5.png)

---

# Task 5 — Run a Live Query to Prove Real Board Data

## Goal

Ask Claude to list the issues in your current active sprint through the Jira MCP connection, and confirm the result matches what you see on your live board in the browser.

### Evidence

#### Screenshot 5 — Claude's response showing the live sprint issue list retrieved via Jira MCP

![alt text](screenshots\sc5-T5-ass5-week5.png)

### Notes You Must Write (Very Important):

How did you confirm this was real board data and not something Claude guessed?

When Claude interacts with your Jira board, you can verify the data is real using these 3 quick checks:

Tool Usage Logs: Look for active tool execution calls (e.g., jira_search_issues or mcp-atlassian) before the response.

System Metadata: Real API responses include exact Jira Issue Keys (PROJ-123), precise timestamps, and Atlassian Account IDs that cannot be guessed.

Live UI Verification: Check /mcp to ensure Jira shows ✔ connected, or cross-reference the issue directly in your Jira browser tab.

---

# Task 6 — Build the /sprint-health Skill

## Goal

Create a `/sprint-health` skill restricted to read-only Jira tools plus `Read`, with no issue-mutating tools and no `Write`. Run it and confirm it produces a report covering sprint velocity, at-risk stories, and items missing an estimate.

### Evidence

#### Screenshot 6 — `SKILL.md` frontmatter showing `allowed-tools` limited to read-only Jira tools plus `Read`, with `disable-model-invocation: true`

![alt text](screenshots\sc5-T5-ass5-week5.png)

#### Screenshot 7 — `/sprint-health` output showing the full triage report against your real sprint

![alt text](screenshots\sc7-T7-ass5-week5.png)

### Notes You Must Write (Very Important):

1. Which Jira MCP tools does this skill's allowed-tools list include, and which mutating tools (create issue, update issue, transition issue, add comment) does it deliberately exclude?

The /sprint-health skill includes only read-only and analytical tools required to inspect project status, retrieve tickets, and assess active sprint health without changing any data:

jira - Get All Projects / jira - Search Projects

jira - Get Agile Boards

jira - Get Sprint Issues / jira - Get Active Sprint

jira - Search Issues (JQL queries) / jira - Get Issue

jira - Get Project Fields

2. Why does a Scrum Master need this restriction more than almost any other role in this course?

1. Jira MCP Tools: Allowed vs. ExcludedAllowed Tools (Read-Only / Inspection):The skill includes only read-only and analytical tools required to inspect sprint status without modifying project data:jira - Search Projects / Get All Projectsjira - Get Agile Boardsjira - Search Issues (via JQL) / Get Issuejira - Get Sprint Issues / Get Active Sprintjira - Get Project FieldsDeliberately Excluded Mutating Tools:The following state-modifying tools are deliberately excluded:create_issue (Creating new tickets)update_issue (Modifying attributes, estimates, or story points)transition_issue (Moving ticket status: To Do $\rightarrow$ In Progress $\rightarrow$ Done)add_comment (Adding comments to tickets)2. Why a Scrum Master Needs This RestrictionA Scrum Master requires this read-only restriction for two fundamental agile reasons:Upholding the Servant Leader Role and Neutrality:The Scrum Master's responsibility is to facilitate, observe, and clear blockers.They must not unilaterally alter the board, reassign story points, or transition tickets on behalf of the developers.Preserving Team Ownership and Data Integrity:Only the Development Team members should update their own ticket statuses (transition_issue) and estimate their work (update_issue).Using AI as a Scrum Master should purely serve as an analytical and reporting tool (/sprint-health), preventing accidental mutations to the live Jira board state during a sprint.

---

# Task 7 — Prove the Skill Never Mutates the Board

## Goal

Manually update one ticket on your board in the browser (for example, move a story to "Done" or add a missing estimate), then run `/sprint-health` again and confirm the new report reflects your change — proving the skill only ever reads live state and never wrote to the board itself.

### Evidence

#### Screenshot 8 — Second `/sprint-health` run showing the report now reflects your manual board change

![alt text](screenshots\sc7-T7-ass5-week5.png)

### Notes You Must Write (Very Important):

Map this assignment to Gather → Analyze → Human Act → Verify from Week 3 Assignment 6. Which step did you perform manually in the browser, and why must that step stay human?

1. Mapping to the Framework
Gather:
The AI (via Jira MCP read-only tools like jira - Search Issues / jira - Get Project Fields) queried your Jira Cloud instance to fetch project keys, active sprint statuses, and ticket details (DEO-20, DEO-4, DEO-3, DEO-2).

Analyze:
The AI evaluated the sprint health, calculating metrics like velocity, story point distribution, at-risk tasks, and unestimated items to generate the sprint summary report.

Human Act:
You manually created the Jira project (DEO), added the issues, assigned story points, and clicked "Start sprint" directly inside the Jira Cloud web browser UI.

Verify:
You re-ran the /sprint-health command (or executed the direct JQL prompt) in Claude Code to confirm that the MCP server successfully read the updated, live sprint state and reflected the correct metrics.

2. Which step was performed manually in the browser?
The Human Act step was performed manually in the browser (creating the project/issues and clicking Start sprint).

3. Why must that step stay human?
This step must remain human for two crucial reasons:

Governance & Accountability:
Starting a sprint and setting commitments represents a formal team agreement. Allowing an AI agent to initiate sprints or create/modify work items autonomously creates accountability gaps and risks accidental, unauthorized changes to the production backlog.

Scrum Principles & Team Ownership:
In Scrum, only the Development Team and Product Owner have the authority to commit to sprint scopes and estimate work. Keeping state-changing actions (Human Act) in human hands ensures the AI remains a supportive facilitator (/sprint-health analysis) rather than an uncontrolled decision-maker.

---

# Submission Instructions

Complete all tasks in sequence.

Your submission must include:
- All 8 required screenshots
- All the required notes

---

# Completion Checklist

- [ ] Task 1: Jira API token created, value never screenshotted (Screenshot 1)
- [ ] Task 2: `.mcp.json` has the Jira server block (Screenshot 2)
- [ ] Task 3: Credentials stored in `settings.local.json`, token blurred, file gitignored (Screenshot 3)
- [ ] Task 4: `/mcp` shows the Jira server connected (Screenshot 4)
- [ ] Task 5: Live query returned real sprint data, verified against the browser (Screenshot 5)
- [ ] Task 6: `/sprint-health` skill created with correct read-only `allowed-tools`, and produced a full report (Screenshots 6–7)
- [ ] Task 7: A manual board change was reflected in a second `/sprint-health` run (Screenshot 8)
- [ ] Skill never created, edited, transitioned, or commented on any issue
- [ ] Reflection answered (Notes)
- [ ] No API token value exposed

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
