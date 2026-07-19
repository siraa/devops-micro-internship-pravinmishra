# Assignment 5 — Bash Script Automation Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will practice Bash scripting by building a series of small automation scripts covering environment setup, variables, arrays, loops, file conditionals, if-else logic, and functions. These scripts form the foundation of real-world Linux automation used in DevOps, cloud, and production support environments.

---

# Task 1 — Bash Environment & Workspace Setup

## Goal

Verify that Bash is available on your system and create a clean workspace for this assignment.

### Evidence

#### Screenshot 1 — Output of `echo $SHELL` and `bash --version`



![alt text](screenshots\sc1-T1-ass5-week3.png)

![alt text](screenshots\sc1.1-T1-ass5-week3.png)
  
---

#### Screenshot 2 — Output of `pwd` and `ls -lah` showing the scripts directory



![alt text](screenshots\sc2-T1-ass5-week3.png)

---

### Notes

Answer the following in your own words:

**1. What is Bash?**



At its core, Bash (short for Bourne-Again SHell) is two things at once: a command-line interpreter and a scripting language.

It is the default terminal interface you encounter when you log into almost any modern Linux distribution (including your Ubuntu server on AWS) and older versions of macOS.
Automating Server Setups: Writing a script that installs Nginx, configures a firewall, and pulls code from Git automatically when a new AWS instance boots up.
---

**2. What is the difference between shell and Bash?**



To put it simply: "Shell" is a general category, while "Bash" is a specific product within that category.

Think of it like cars. A "shell" is the concept of a motor vehicle—it defines the shape, purpose, and how you drive it. "Bash" is a specific make and model, like a Toyota Camry or a Ford Mustang. It’s one specific type of car that you can choose to drive.

---

**3. Why is it important to confirm the Bash version before writing scripts?**



Confirming your Bash version before writing or running scripts is a crucial step in ensuring your automation is portable, reliable, and free of unexpected runtime syntax errors.

Just like Python, Java, or JavaScript, Bash is an evolving language. Features added in modern versions of Bash will completely break if executed on an older system.
To ensure your script explicitly forces the system to interpret it using the true Bash binary rather than a generic, stripped-down shell, always start your scripts with the absolute path to the Bash interpreter
---

# Task 2 — Your First Bash Script

## Goal

Create your first Bash script, make it executable, and run it from the terminal.

### Evidence

#### Screenshot 1 — Content of `first-script.sh`



![alt text](screenshots\sc1-T2-ass5-week3.png)
---

#### Screenshot 2 — Output of `./first-script.sh`




![alt text](screenshots\sc2-T2-ass5-week3.png)
---

#### Screenshot 3 — Output of `ls -l first-script.sh` showing executable permission



![alt text](screenshots/sc3-T2-ass5-week3.png)
---

### Notes

Answer the following in your own words:

**1. What is the purpose of `#!/bin/bash`?**



That line—#!/bin/bash—is called a Shebang (or a hash-bang). It must always be the absolute first line of your script file.

Its purpose is simple: it tells the Linux operating system exactly which program (interpreter) to use to execute the commands written inside that file.
---

**2. Why do we use `chmod +x` before running a script?**



By default, when you create a new text file in Linux (like a Bash script), the operating system treats it strictly as data—a file to be read or written, not a program to be run.

Running chmod +x changes the file's metadata permissions, explicitly telling Linux: "This file is safe to execute as an application."
---

**3. What is the difference between running a script using `./script.sh` and `bash script.sh`?**



While both methods result in running your script, they execute it in fundamentally different ways behind the scenes.

The core difference lies in how the shell finds the interpreter and whether the file requires execution permissions.
---

# Task 3 — Variables: User Information Script

## Goal

Use variables to store and display user-related information.

### Evidence

#### Screenshot 1 — Content of `user-info.sh`



![alt text](screenshots\sc1-T2-ass5-week3.png)
---

#### Screenshot 2 — Output of `./user-info.sh`



![alt text](screenshots\sc2-T3-ass5-week3.png)
---

### Notes

Answer the following in your own words:

**1. What is a variable in Bash?**



In Bash, a variable is a temporary storage location in your system's memory that holds a piece of text or a number. You assign a name to this location so you can easily reference, modify, or reuse that data throughout your script.

In DevOps, variables are the foundation of automation. Instead of hardcoding things like IP addresses, file paths, or usernames directly into your scripts, you store them in variables so your code remains flexible.
---

**2. Why should we avoid spaces around the `=` sign when creating variables?**



It all comes down to how the Bash parser reads commands.

Unlike other programming languages (such as Python or JavaScript) that use a compiler to analyze the entire line of code first, Bash is a line-by-line command interpreter. Its first and most basic rule when reading a line is: "The very first word I see is the command to run, and everything after it is an argument."

When you add spaces around the = sign, you confuse the parser into thinking you are trying to execute a system command rather than assign a variable.
---

**3. How do you access the value stored inside a Bash variable?**


To access the value stored inside a Bash variable, you use the $ (dollar sign) symbol.

While assigning a value is done using the variable name alone, reading or "referencing" that value always requires the $ prefix. This tells the Bash parser: "Don't treat this word as literal text; go look up its value in memory."
---

# Task 4 — Arrays & Loops: Tools Checklist Script

## Goal

Use arrays and loops to print a checklist of tools used in Bash scripting.

### Evidence

#### Screenshot 1 — Content of `tools-checklist.sh`



![alt text](screenshots\sc1-T4-ass5-week3.png)
---

#### Screenshot 2 — Output of `./tools-checklist.sh`


![alt text](screenshots\sc2-T4-ass5-file-week3.png)
---

### Notes

Answer the following in your own words:

**1. What is an array in Bash?**



An array in Bash is a variable that lets you store multiple values under a single name.

Instead of creating separate variables like SERVER1, SERVER2, and SERVER3, you can create a single array variable called SERVERS to hold all of them. This is incredibly useful in DevOps for looping through lists of IP addresses, package names, or directories that you need to process in bulk.
---

**2. Why are arrays useful in scripts?**



In DevOps and automation, writing clean, maintainable code is all about avoiding repetitive tasks.

If you didn't have arrays, your scripts would be cluttered with duplicate code and difficult to scale. Arrays transform how you write scripts by offering three massive advantages: the DRY principle, dynamic scalability, and seamless integration with loops.

---

**3. What does `"${tools[@]}"` mean?**



The syntax "${tools[@]}" is one of the most important concepts to master in Bash scripting. It is the standard, safest way to say: "Give me every single item inside the tools array, kept exactly as they are."

---

**4. What is the purpose of the `for` loop in this script?**



The purpose of a for loop is automation through repetition. It allows you to write a block of code just once and have Bash execute it over and over again for every item in a list.

Instead of manually duplicating your commands for every single tool, file, or IP address, the for loop acts as an assembly line that automatically processes items one by one.

---

# Task 5 — Loops: Number Counter Script

## Goal

Use loops to repeat a task multiple times.

### Evidence

#### Screenshot 1 — Content of `counter.sh`



![alt text](screenshots\sc1-T5-ass5-week3.png)
---

#### Screenshot 2 — Output of `./counter.sh`



![alt text](screenshots\sc2-T5-ass5-week3.png)
---

### Notes

Answer the following in your own words:

**1. What is a loop?**


A loop is a fundamental programming structure that tells your computer to repeat a block of code multiple times under specified conditions.

Instead of copy-pasting the same lines of code over and over again, a loop allows you to write the instructions exactly once and let the computer handle the repetition. Computers are incredibly fast and never get tired, making loops the ultimate tool for handling repetitive, bulk tasks.

---

**2. Why do we use loops in Bash scripting?**



We use loops in Bash scripting for one primary reason: to automate repetitive tasks efficiently and consistently at scale.

In DevOps and systems administration, manual repetition is the enemy. It wastes time, causes fatigue, and inevitably introduces human error. Loops allow you to write a block of instruction once
---

**3. How many times did the loop run in your script?**



In the system audit script I wrote just a few prompts ago, the loop ran exactly 5 times.
---

**4. What would you change if you wanted the loop to run 10 times?**



I would add the numbers 6 to 10 to the for loop:
for number in 1 2 3 4 5 6 7 8 9 10
do
   	echo "Step $number completed"
done


---

# Task 6 — Files & Conditionals: File Validation Script

## Goal

Use file checks and conditionals to verify whether files and directories exist.

### Evidence

#### Screenshot 1 — Output of `ls -lah ../test-folder`



![alt text](screenshots\sc1-T6-ass5-week3.png)
---

#### Screenshot 2 — Content of `file-check.sh`



![alt text](screenshots\sc2-T6-ass5-week3.png)
---

#### Screenshot 3 — Output of `./file-check.sh`



![alt text](screenshots\sc3-T6-ass5-week3.png)
---

### Notes

Answer the following in your own words:

**1. What does `-d` check in Bash?**



The -d option checks whether the given path exists and whether it is a directory. If the directory exists, the condition becomes true.

---

**2. What does `-f` check in Bash?**


The -f option checks whether the given path exists and whether it is a regular file. If the file exists, the condition becomes true.

---

**3. Why should file and directory paths be stored in variables?**
.

Storing paths in variables makes the script easier to read and update. If a path changes, we only need to update the variable instead of changing the same path in several places.

---

**4. What happens if the file does not exist?**



If the file does not exist, the -f check becomes false. Therefore, the commands under else will run, and the following message will be displayed:
File does not exist: ../test-folder/student-info.txt

---

# Task 7 — Conditionals: Pass or Retry Script

## Goal

Use if-else conditionals to make decisions based on a variable value.

### Evidence

#### Screenshot 1 — Content of `score-check.sh` with `score=85`



![alt text](screenshots\sc1-T7-ass5-week3.png)
---

#### Screenshot 2 — Output showing `Result: Pass`



![alt text](screenshots\sc2-T7-ass5-week3.png)
---

#### Screenshot 3 — Content of `score-check.sh` with `score=55`



![alt text](screenshots\sc3.1-T7-ass5-week3.png)



---

#### Screenshot 4 — Output showing `Result: Retry`



![alt text](screenshots\sc3.2-T7-ass5-week3.png)
---

### Notes

Answer the following in your own words:

**1. What is the purpose of if-else in Bash?**



An if-else statement allows the script to make a decision. If the given condition is true, it runs one set of commands. If the condition is false, it runs another set of commands.

---

**2. What does `-ge` mean?**



-ge means greater than or equal to. In this script, it checks whether the score is greater than or equal to 70.
[ "$score" -ge 70 ]

---

**3. Why should conditions be tested with different values?**



We should test conditions with different values to make sure every possible result works correctly. Here, we use 85 to test the Pass result and 55 to test the Retry result.
It is also helpful to test the exact boundary value, 70, because it should produce Pass.

---

**4. How can conditionals help in automation scripts?**



Conditionals help automation scripts decide what to do based on the current situation. For example, a script can check whether a service is running, a file exists, or a disk is almost full, and then take the correct action based on the result.
---

# Task 8 — Functions: Final Bash Automation Script

## Goal

Create a final Bash script using functions to organize reusable code.

### Evidence

#### Screenshot 1 — Content of `final-automation.sh`

![alt text](screenshots\sc1-T8-ass5-week3.png)

---

#### Screenshot 2 — Output of `./final-automation.sh`

![alt text](screenshots\sc2-T8-ass5-week3.png)

---

#### Screenshot 3 — Output of `ls -lah` showing all created scripts

![alt text](screenshots\sc3-T8-ass5-week3.png)

---

### Notes

Answer the following in your own words:

**1. What is a function in Bash?**

A function in Bash is a reusable block of code that you write once and can then call multiple times from anywhere in your script.

Think of it as a custom command you build yourself. Instead of writing the same 10 lines of code every time you need to log a message, connect to a database, or check system health, you package those lines inside a function and run it by simply calling its name.

---

**2. Why are functions useful in scripts?**



Functions help us separate a large script into smaller sections. This makes the script easier to read, manage, and troubleshoot. If we need the same task more than once, we can call the function again instead of rewriting its commands.

---

**3. Which functions did you create in this script?**


I created four functions:
print_header prints the assignment header.
print_user_details prints my full name and the assignment name.
check_files checks whether the required directory and file exist.
print_tools uses a loop to print each tool stored in the array.

---

**4. How does this final script combine variables, arrays, loops, conditionals, files, and functions?**



The script uses variables to store my name, the assignment name, and the required paths. It uses an array to store the tool names and a loop to print them one by one.
It uses if-else conditionals with -d and -f to check the required directory and file. Finally, the related commands are organized into functions, and those functions are called in the correct order to run the complete automation script.

---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

<<<<<<< HEAD
https://www.linkedin.com/posts/taysir-ouaslati-9b6527a3_moving-from-manual-commands-to-bulletproof-activity-7483642510712586241-5EQf?utm_source=share&utm_medium=member_desktop&rcm=ACoAABX4AtoB0tpceeC8Jnqozhzdi2ViZ02bFHk

=======
`Add your URL here`
>>>>>>> upstream/main

---

#### Screenshot — Published LinkedIn post

![alt text](screenshots\post-linked-ass5.png)

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- All script files must be created and run successfully
- Required notes must be answered clearly for every task
- Do not expose sensitive information (keys, passwords, credentials)

---

# Completion Checklist

- [ ] Task 1: Environment setup verified, workspace created (Screenshots 1–2, Notes answered)
- [ ] Task 2: First script created, executed, permissions verified (Screenshots 1–3, Notes answered)
- [ ] Task 3: Variables script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 4: Arrays and loops script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 5: Counter loop script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 6: File validation script created and run (Screenshots 1–3, Notes answered)
- [ ] Task 7: Pass/Retry conditional script tested with both values (Screenshots 1–4, Notes answered)
- [ ] Task 8: Final automation script created and run (Screenshots 1–3, Notes answered)
- [ ] All scripts run without errors
- [ ] Full Name visible in all required screenshots
- [ ] LinkedIn post published and URL submitted
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