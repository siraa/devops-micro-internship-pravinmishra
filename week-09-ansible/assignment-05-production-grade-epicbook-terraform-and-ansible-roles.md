# Assignment — Deploy EpicBook with Terraform and Ansible Roles

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will deploy the EpicBook web application using Terraform and Ansible roles.

Terraform provisions the cloud infrastructure, including one Ubuntu VM and one managed MySQL database. Ansible roles configure the VM, install required software, deploy the EpicBook application, configure Nginx, connect the app to the managed MySQL database, and verify the deployment.

---

# Task 1 — Set Up the Project Folder Layout

## Goal

Create the project folder structure for Terraform and Ansible roles.

Terraform will be used to provision the cloud infrastructure. Ansible roles will be used to configure the VM and deploy the EpicBook application.

### Evidence

#### Screenshot 1 — Terminal showing the completed `epicbook-prod` project structure

![alt text](screenshots\sc1.1-T1-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. Which cloud provider did you choose for this assignment?**

I chose Amazon Web Services (AWS) as my cloud provider. I used AWS to host the EpicBook application and its database.

---

**2. Why is it useful to keep Terraform files and Ansible files in separate folders?**

Keeping Terraform and Ansible files in separate folders makes the project more organized. Terraform is responsible for creating the cloud infrastructure, while Ansible is responsible for configuring the servers and deploying the application. Separating them makes the project easier to manage and maintain.

---

**3. What is the purpose of the `roles` directory in Ansible?**

The roles directory organizes Ansible tasks into reusable groups. In my project, I used separate roles for common packages, Nginx configuration, and EpicBook deployment. This makes the automation easier to understand and maintain.

---

# Task 2 — Provision the Infrastructure with Terraform

## Goal

Run Terraform to provision the cloud infrastructure for the EpicBook deployment.

Terraform will create the VM, managed MySQL database, networking, security rules, and required outputs.

### Evidence

#### Screenshot 2 — `terraform apply` completed successfully

![alt text](screenshots\sc2-T2-apply-ass5-week9.png)

---

#### Screenshot 3 — Output of `terraform output`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 4 — Azure Portal or AWS Console showing the VM running

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 5 — Azure Portal or AWS Console showing the managed MySQL database created

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What resources did Terraform create for this assignment?**

Terraform created AWS infrastructure for the EpicBook project, including an EC2 instance, networking resources, security groups, and an RDS MySQL database. It also configured the required outputs, such as the public IP address and database connection information.

---

**2. Why should you review `terraform plan` before running `terraform apply`?**

Reviewing terraform plan helps me understand which resources Terraform will create, modify, or destroy before making changes. It helps prevent unexpected changes and avoids accidentally deleting important resources.

---

**3. Why should database passwords not be shown in Terraform output?**

Database passwords should not be displayed because they are sensitive information. Exposing them could allow unauthorized people to access the database. They should be stored securely and protected using tools such as Ansible Vault.

---

# Task 3 — Verify SSH Key-Based Access

## Goal

Verify that the cloud VM can be accessed from the Ansible controller using SSH key-based authentication.

### Evidence

#### Screenshot 6 — Successful SSH hostname check from the Ansible controller

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What command did you use to verify SSH access?**

ansible web -i inventory.ini -m ping

---

**2. What proves that SSH key-based access worked successfully?**

The result SUCCESS with "ping": "pong" proves that Ansible successfully connected to the EC2 instance using SSH key-based authentication.

---

**3. What would you check if SSH returned `Permission denied (publickey)`?**

I would check that the SSH username is correct, the private key path is valid, the correct public key is installed on the EC2 instance, and the private key has the correct permissions. I would also verify that the security group allows SSH traffic on port 22 from my public IP address.

---

# Task 4 — Create the Ansible Inventory and Configuration

## Goal

Create the Ansible inventory file and local Ansible configuration for the EpicBook VM.

The inventory tells Ansible which VM to manage and which SSH user to use.

### Evidence

#### Screenshot 7 — `inventory.ini` showing the VM under the `web` group

Add your screenshot here.

---

#### Screenshot 8 — Output of `ansible-inventory -i inventory.ini --graph`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 9 — Output of `ansible web -i inventory.ini -m ping`

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `inventory.ini`?**

The inventory.ini file tells Ansible which servers it needs to manage. In my project, it contains the EC2 instance information and the SSH connection settings.

---

**2. What does `ansible_host` store?**

ansible_host stores the IP address or hostname of the remote server. In my project, it contains the public IP address of the EC2 instance.

---

**3. What does `ansible_ssh_private_key_file` tell Ansible?**

It tells Ansible where to find the private SSH key used to connect securely to the EC2 instance.

---

**4. Why is `host_key_checking = False` used only for this temporary lab?**

It disables SSH host key verification, which makes initial connections easier in a temporary lab. However, it reduces security because Ansible does not verify the server's identity. In production, host key checking should remain enabled.

---

# Task 5 — Create the Main Ansible Playbook

## Goal

Create the main Ansible playbook that runs the required roles in the correct order.

The `site.yml` file will call the `common`, `nginx`, and `epicbook` roles.

### Evidence

#### Screenshot 10 — `site.yml` showing the roles in the correct order

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 11 — Output of `ansible-playbook -i inventory.ini site.yml --syntax-check`

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `site.yml`?**

The site.yml file is the main Ansible playbook. It runs the required roles to configure the EC2 instance, install Nginx, and deploy the EpicBook application.

---

**2. Why should the roles run in the order `common`, `nginx`, and `epicbook`?**

The common role installs the basic packages needed by the server. The Nginx role installs and configures the web server. Finally, the EpicBook role installs the application dependencies, configures the database connection, imports the SQL files, and starts the application with PM2. This order ensures that the required components are ready before deploying the application.

---

**3. What does `become: true` allow Ansible to do?**

become: true allows Ansible to execute tasks with elevated privileges, usually as the root user. This is necessary for operations such as installing packages, modifying system files, and configuring services.

---

# Task 6 — Create the `common` Role

## Goal

Create the `common` role to prepare the Ubuntu VM with the basic packages required for the EpicBook deployment.

This role handles the common server setup before Nginx and the application are configured.

### Evidence

#### Screenshot 12 — `roles/common/tasks/main.yml` showing the common setup tasks

![alt text](screenshots\sc12-T6-ass9-week9.png)

---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `common` role?**

The common role prepares the server by updating the package cache and installing basic packages required for the deployment. It makes sure the EC2 instance has the necessary tools before installing and configuring the application.

---

**2. Why should Nginx installation not be placed inside the `common` role?**

Nginx has its own specific configuration and responsibilities. Keeping it in a separate role makes the project more organized and easier to maintain. It also allows the Nginx role to be reused independently in other projects

---

**3. Why is `mysql-client` useful in this deployment?**

mysql-client allows the EC2 instance to connect to the remote MySQL database hosted on Amazon RDS. It is used to create the database, import SQL schema and seed files, and verify the database connection without installing a MySQL server on EC2.

---

# Task 7 — Create the `nginx` Role

## Goal

Create the `nginx` role to install Nginx and configure it as a reverse proxy for the EpicBook application.

Nginx will receive browser traffic on port `80` and forward it to the EpicBook Node.js application running on the VM.

### Evidence

#### Screenshot 13 — `roles/nginx/tasks/main.yml` showing Nginx installation and site configuration tasks

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 14 — `roles/nginx/templates/epicbook.conf.j2` showing the reverse proxy configuration

![alt text](week-09-ansible\screenshots\sc14-T7-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `nginx` role?**

The nginx role installs and configures Nginx on the EC2 instance. It deploys the EpicBook website configuration, enables the site, disables the default Nginx site, and makes sure the Nginx service is running.

---

**2. Why is Nginx configured as a reverse proxy in this deployment?**

Nginx is configured as a reverse proxy to receive HTTP requests from users on port 80 and forward them to the EpicBook Node.js application running on port 8080. This allows users to access the application through the public IP address without connecting directly to the application port.

---

**3. Why should the application port come from `group_vars/web.yml` instead of being hard-coded?**

Using a variable for the application port makes the configuration easier to manage and reuse. If the port changes, I only need to update the variable instead of modifying several files. It also keeps the configuration consistent across the Ansible roles and tasks.

---

# Task 8 — Create the `epicbook` Role

## Goal

Create the `epicbook` role to deploy the EpicBook application, connect it to the managed MySQL database, and run the application on port `8080` using PM2.

### Evidence

#### Screenshot 15 — `roles/epicbook/tasks/main.yml` showing application deployment tasks

![alt text](screenshots\sc15-T8-ass5-week9.png)

---

#### Screenshot 16 — Task or file showing how the database connection is configured, with secrets hidden

![alt text](screenshots\sc16.2-T16-ass5-week9.png)

---

#### Screenshot 17 — Task or output showing the EpicBook application managed by PM2

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What is the responsibility of the `epicbook` role?**

The epicbook role deploys and configures the EpicBook application on the EC2 instance. It installs Node.js and PM2, clones the application repository from GitHub, installs the required dependencies, connects the application to the RDS database, imports the SQL schema and seed data, and starts the application.

---

**2. Why is PM2 used for the EpicBook Node.js application?**

PM2 is used to manage the Node.js application in production. It keeps the application running in the background, monitors its status, and allows it to restart if it stops or crashes. It also makes it easier to manage and restart the application.

---

**3. Why should database passwords not be hard-coded in public files?**

Database passwords should not be hard-coded because public files can expose sensitive information. Unauthorized people could use these passwords to access the database. In my project, I used Ansible Vault to store the database password securely.

---

**4. What does it mean for the application to run on port `8080` while Nginx listens on port `80`?**

It means that the EpicBook Node.js application listens internally on port 8080, while Nginx receives public HTTP requests on port 80. Nginx acts as a reverse proxy and forwards requests from port 80 to port 8080, allowing users to access the website through the EC2 public IP address.

---

# Task 9 — Create Group Variables

## Goal

Create reusable variables for the EpicBook deployment.

The `group_vars/web.yml` file stores values that can be reused across the Ansible roles.

### Evidence

#### Screenshot 18 — `group_vars/web.yml` showing the application, PM2, and database variables, with passwords hidden or masked

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `group_vars/web.yml`?**

The purpose of group_vars/web.yml is to define variables that are shared by all hosts in the web group. It helps keep the configuration organized and avoids repeating the same values in different Ansible tasks and roles.

---

**2. Which values did you store in `group_vars/web.yml`?**

I stored application and database configuration values, such as the application name, application directory, application port, repository URL, database host, database port, database name, and database username.

---

**3. How did you handle the database password securely?**

I stored the database password in a separate encrypted file called vault.yml using Ansible Vault. This prevents the password from being stored as plain text in the Ansible configuration files. I used --ask-vault-pass when running the playbooks to access the encrypted password securely.

---

# Task 10 — Run the Ansible Playbook

## Goal

Run the Ansible playbook to configure the VM and deploy the EpicBook application.

The playbook should run the roles in this order:

1. `common`
2. `nginx`
3. `epicbook`

### Evidence

#### Screenshot 19 — Ansible playbook output showing the roles running

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 20 — Final Ansible recap showing `failed=0`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 21 — Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx" --become`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 22 — Output of `ansible web -i inventory.ini -m command -a "pm2 status"`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 23 — Output of `ansible web -i inventory.ini -m command -a "curl -I http://localhost:8080"`

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What command did you run to execute the Ansible playbook?**

I used the following command to run the main Ansible playbook:

ansible-playbook -i inventory.ini site.yml --ask-vault-pass

---

**2. How do you know all roles completed successfully?**

The playbook finished without errors. The final recap showed failed=0 and unreachable=0, which means all tasks completed successfully and Ansible could connect to the EC2 instance.

---

**3. What proves that Nginx is active?**

The Ansible playbook completed the Nginx tasks successfully, including ensuring that the Nginx service was started and enabled. The successful HTTP response from the public IP address also confirms that Nginx is serving requests.

---

**4. What proves that PM2 is managing the EpicBook application?**

I ran the command ansible web -i inventory.ini -m command -a "pm2 status". The output showed the epicbook process with the status online under the ubuntu user. This confirms that PM2 is managing the application.

---

**5. What proves that the EpicBook application responds on port `8080`?**

The Ansible playbook successfully completed the task Wait for EpicBook to respond on the application port. This confirms that the application responded on port 8080.

---

# Task 11 — Verify the EpicBook Deployment

## Goal

Verify that the EpicBook application is running, accessible in the browser, and connected to the managed MySQL database.

### Evidence

#### Screenshot 24 — Output of `curl -I http://<public_ip>`

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 25 — Output of the cart API test command

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 26 — Output of the `/cart` HTTP status check

![alt text](screenshots\output-ass5-week9.png)

---

#### Screenshot 27 — Browser showing the EpicBook application loaded from `http://<public_ip>`

![alt text](screenshots\output-ass5-week9.png)

---

### Notes

Answer the following in your own words:

**1. What HTTP response did you receive from the public application URL?**

I received an HTTP/1.1 200 OK response when I ran curl -I http://18.153.95.88. This confirmed that the EpicBook website was publicly accessible through the EC2 instance.

---

**2. What did the cart API test prove?**

The cart API test checked whether the application's cart endpoint was responding correctly. A successful response confirmed that the application could handle cart-related API requests.

---

**3. What did the `/cart` status check return?**

The /cart status check verified that the cart endpoint was accessible and returned a successful HTTP response.

---

**4. What issue did you face during verification, and how did you fix it?**

During verification, I initially had a database name mismatch. The SQL files expected a database named bookstore, while Ansible was using appdb. I fixed this by changing the Ansible db_name variable to bookstore. After rerunning the playbook, the SQL imports completed successfully, PM2 started the application, and the public website returned HTTP 200 OK.

---

# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://lnkd.in/p/d_3UX4CR

---

#### Screenshot — Published LinkedIn post

![alt text](screenshots\linkedIn-ass5-week9.png)

---

# Assignment Questions

Answer the following in your own words:

**1. Why is Terraform used for infrastructure provisioning?**

Terraform is used to create and manage cloud infrastructure automatically. In my project, I used it to provision AWS resources such as the EC2 instance, networking, security groups, and the RDS MySQL database. It also makes the infrastructure easier to reproduce and manage.

---

**2. Why are Ansible roles useful for production-style deployments?**

Ansible roles organize tasks into separate and reusable components. In my project, I used different roles for common packages, Nginx, and EpicBook. This makes the deployment easier to maintain, troubleshoot, and reuse.

---

**3. What is the purpose of `group_vars/web.yml`?**

The purpose of group_vars/web.yml is to store variables shared by the hosts in the web group. It keeps configuration values in one place and avoids repeating them in different playbooks and roles.

---

**4. Why should database passwords not be committed to GitHub?**

Database passwords should not be committed to GitHub because repositories may be public or accessed by unauthorized people. Exposing passwords could allow attackers to access the database. In my project, I used Ansible Vault to encrypt the database password.

---

**5. What is the purpose of Nginx in this deployment?**

Nginx acts as a web server and reverse proxy. It receives HTTP requests on port 80 and forwards them to the EpicBook Node.js application running on port 8080. This allows users to access the website through the EC2 public IP address.

---

**6. Why should the managed MySQL database not be publicly accessible?**

The MySQL database should not be publicly accessible because this increases the risk of unauthorized access and attacks. It is safer to allow database connections only from trusted resources, such as the EC2 instance, through security group rules.

---

**7. Why is PM2 used for the EpicBook Node.js application?**

PM2 manages the Node.js application and keeps it running in the background. It monitors the application, allows automatic restarts if it crashes, and makes it easier to manage the application process.

---

**8. What does idempotency mean in Ansible?**

Idempotency means that running the same Ansible playbook multiple times should not create unnecessary changes if the system is already configured correctly. It helps keep the server in the desired state and makes automation more reliable.

---

**9. What issue did you face during the deployment, and how did you fix it?**

I faced a database name mismatch during the SQL import. The SQL files expected a database named bookstore, but Ansible was using appdb. I fixed this by changing the db_name variable in group_vars/web/vars.yml to bookstore. After running the playbook again, the database import succeeded and the application started correctly.

---

**10. What security improvement would you make before using this setup in production?**

Before using this setup in production, I would enable HTTPS with an SSL/TLS certificate, keep SSH access restricted to trusted IP addresses, enable SSH host key checking, and improve secret management. I would also review security group rules and enable regular backups for the RDS database.

---

# Required Files

Confirm that the following files are included in your GitHub repository or assignment folder:

- [ ] `README.md`
- [ ] Terraform files under either `terraform/azure/` or `terraform/aws/`
- [ ] `ansible/ansible.cfg`
- [ ] `ansible/inventory.ini`
- [ ] `ansible/site.yml`
- [ ] `ansible/group_vars/web.yml`
- [ ] `ansible/roles/common/tasks/main.yml`
- [ ] `ansible/roles/nginx/tasks/main.yml`
- [ ] `ansible/roles/nginx/templates/epicbook.conf.j2`
- [ ] `ansible/roles/epicbook/tasks/main.yml`

---

# Submission Instructions

- Add all required screenshots in your submission.
- Full Name must be visible in required screenshots.
- Mention the cloud provider used: Azure or AWS.
- Add the VM public IP address.
- Add the final application URL.
- Add Terraform output proof.
- Add Ansible role tree proof.
- Add all required notes and assignment question answers.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, cloud credentials, database credentials, Terraform state files, subscription IDs, or account IDs.

---

# Completion Checklist

- [ ] Task 1: Project folder layout created
- [ ] Task 2: Terraform infrastructure provisioned
- [ ] Task 3: SSH key-based access verified
- [ ] Task 4: Ansible inventory and configuration created
- [ ] Task 5: Main Ansible playbook created
- [ ] Task 6: `common` role created
- [ ] Task 7: `nginx` role created
- [ ] Task 8: `epicbook` role created
- [ ] Task 9: Group variables created
- [ ] Task 10: Ansible playbook run completed
- [ ] Task 11: EpicBook deployment verified
- [ ] Terraform files created under only one cloud provider folder
- [ ] One Ubuntu VM was created
- [ ] One managed MySQL database was created
- [ ] SSH port `22` is restricted to the controller public IP
- [ ] HTTP port `80` is accessible
- [ ] MySQL port `3306` is not publicly open
- [ ] `ansible web -i inventory.ini -m ping` returns `SUCCESS`
- [ ] `site.yml` calls the roles in the correct order
- [ ] Database secrets are hidden or handled securely
- [ ] Nginx is active
- [ ] PM2 shows the EpicBook application running
- [ ] EpicBook responds on port `8080`
- [ ] Public URL loads in the browser
- [ ] Cart API verification works
- [ ] Playbook completes with `failed=0`
- [ ] Screenshots 1–27 are included
- [ ] Assignment questions are answered
- [ ] LinkedIn post published
- [ ] LinkedIn post URL added
- [ ] No sensitive information is exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra and The CloudAdvisory, focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## Resources

- DMI Official Website: [https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme)
- University: [https://university.pravinmishra.com?utm_source=github&utm_medium=readme](https://university.pravinmishra.com?utm_source=github&utm_medium=readme)
- Discord Community: [https://discord.pravinmishra.com?utm_source=github&utm_medium=readme](https://discord.pravinmishra.com?utm_source=github&utm_medium=readme)
- Blog: [https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*