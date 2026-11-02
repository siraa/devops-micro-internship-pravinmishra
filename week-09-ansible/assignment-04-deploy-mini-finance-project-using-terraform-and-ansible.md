# Assignment 04 — Deploy Mini Finance on Azure Using Terraform and Ansible

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will provision Azure infrastructure using Terraform and deploy the Mini Finance website using an Ansible multi-play playbook.

Terraform will create the Azure Virtual Machine and networking resources. Ansible will install Nginx, clone the Mini Finance repository, deploy the website, and verify the deployment.

---

# Task 1 — Create the Project Structure

## Goal

Create separate directories and files for the Terraform infrastructure and Ansible configuration.

### Evidence

#### Screenshot 1 — Terminal or VS Code showing the complete `mini-finance` project structure

![alt text]( screenshots\sc1-T1-ass4-week9.png)

---

### Notes



---

# Task 2 — Create the Azure Infrastructure Using Terraform

## Goal

Use Terraform to provision an Ubuntu Virtual Machine with the required Azure networking and security resources.

### Evidence

#### Screenshot 2 — Terraform code showing the `Allow-SSH` rule for port `22` and the `Allow-HTTP` rule for port `80`

![alt text](screenshots\sc2-T2-ass4-week9.png)

---

#### Screenshot 3 — Terraform code showing the association between `nsg-mini-finance` and `nic-mini-finance`

![alt text](screenshots\sc3-T2-ass4-week9.png)

---

### Notes

The Ansible playbook site.yml executes three plays in the following order:

Nginx installation: Installs Nginx on the web server.

Web page deployment: Deploys the Mini Finance web page to /var/www/html/index.html.

HTTP verification: Checks from the Ansible controller whether the web server responds correctly to HTTP requests.

Prerequisite: The AWS Security Group must allow incoming HTTP traffic on port 80. The final play checks access to http://98.88.216.4.

To check the playbook syntax before execution, run:

ansible-playbook -i inventory.ini site.yml --syntax-check

Then execute the playbook:

ansible-playbook -i inventory.ini site.yml

 If the HTTP verification fails, make sure Nginx is running and that the AWS Security Group allows incoming TCP traffic on port 80.

---

# Task 3 — Initialize and Apply the Terraform Configuration

## Goal

Format and validate the Terraform configuration, review the execution plan, and provision the Azure infrastructure.

### Evidence

#### Screenshot 4 — End of the `terraform apply` output showing `Apply complete!` with no errors

![alt text](screenshots\sc4-T2-ass4-week9.png)

---

#### Screenshot 5 — Output of `terraform output public_ip` showing the VM’s public IP address

![alt text](screenshots\output-public-ip-ass4-week9.png)

---

### Notes

The terraform output public_ips command displays the public IP addresses assigned to the web servers deployed on AWS.

web1: 98.88.216.4

web2: 32.194.72.31

These public IP addresses can be used to connect to the web servers remotely via SSH and to access the deployed web application through a browser.

Note: The correct Terraform output name is public_ips (plural), not public_ip, because the output contains the public IP addresses of multiple virtual machines.  

---

# Task 4 — Verify Passwordless SSH Access

## Goal

Confirm that the Ansible controller can connect to the Terraform-provisioned Azure VM using SSH key authentication.

### Evidence

#### Screenshot 6 — Passwordless SSH command and the returned `mini-finance` hostname

![alt text](screenshots\ssh-ip-ass4-week9.png)

---

### Notes

The SSH command demonstrates that passwordless SSH authentication is working correctly between the local machine and the AWS EC2 instance.

The connection uses the private key ~/.ssh/id_ed25519 to authenticate with the remote Ubuntu server without requiring a password. The hostname command then displays the hostname returned by the instance.

This confirms that the SSH key is configured correctly and that remote access to the virtual machine is successful.

---

# Task 5 — Create the Ansible Inventory and Verify Connectivity

## Goal

Add the Terraform-provisioned Azure VM to the Ansible inventory and confirm that Ansible can connect to it.

### Evidence

#### Screenshot 7 — Ansible ping output showing `SUCCESS` and `pong` from the Azure VM

![alt text](screenshots\sc4-T2-ass4-week9.png)

---

### Configuration File

Copy and paste the complete contents of your `ansible/inventory.ini` file below:

[web]
98.88.216.4

[web:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/home/thess/.ssh/id_ed25519

---

# Task 6 — Create the Multi-Play Ansible Playbook

## Goal

Create one Ansible playbook containing separate plays to install Nginx, deploy the Mini Finance website, and verify the deployment.

### Evidence

#### Screenshot 8 — `site.yml` showing Play 1 and the beginning of Play 2

Screenshot must show:

- Play 1 targeting the `web` group
- Installation of `nginx`, `git`, and `rsync`
- Nginx service configured as started and enabled
- Beginning of Play 2 with the Git repository URL and synchronization task

![alt text](screenshots\sc8-T6-ass4-week9.png)

---

#### Screenshot 9 — `site.yml` showing the deployment destination, handler, and Play 3 verification

Screenshot must show:

- Website destination `/var/www/html/`
- Ownership set to `www-data:www-data`
- Nginx reload handler
- Play 3 targeting `localhost`
- The `uri` verification and `assert` condition

![alt text](screenshots\sc9-T6-ass4-week9.png)

---

### Configuration File

Copy and paste the complete contents of your `ansible/site.yml` file below:

---
# Play 1: Install and configure Nginx on the web servers
- name: Install and configure Nginx on the web servers
  hosts: web
  become: true

  tasks:
    - name: Update the apt package cache
      ansible.builtin.apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install required packages
      ansible.builtin.apt:
        name:
          - nginx
          - git
          - rsync
        state: present

    - name: Ensure Nginx is started and enabled at boot
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true


# Play 2: Clone the Mini Finance website and deploy it to Nginx's web root
- name: Clone and deploy the Mini Finance website
  hosts: web
  become: true

  handlers:
    - name: Reload Nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded

  tasks:
    - name: Clone or update the Mini Finance repository
      ansible.builtin.git:
        repo: https://github.com/pravinmishraaws/mini-finance-project
        dest: /opt/mini-finance
        version: HEAD
        force: true

    - name: Synchronize website files to the web root
      ansible.posix.synchronize:
        src: /opt/mini-finance/
        dest: /var/www/html/
        delete: false
        rsync_opts:
          - "--exclude=.git"
      delegate_to: "{{ inventory_hostname }}"
      notify: Reload Nginx

    - name: Set owner and group of the web root to www-data
      ansible.builtin.file:
        path: /var/www/html/
        owner: www-data
        group: www-data
        recurse: true


# Play 3: Verify the deployment from the controller (localhost)

- name: Verify the deployment from the controller
  hosts: localhost
  connection: local
  become: false

  tasks:
    - name: Request the website from the web server's public IP
      ansible.builtin.uri:
        url: "http://{{ groups['web'][0] }}/"
        method: GET
        status_code: 200
        return_content: false
      register: site_check

    - name: Assert the website responded with HTTP 200
      ansible.builtin.assert:
        that:
          - site_check.status == 200
        success_msg: "Website is up (HTTP {{ site_check.status }})"
        fail_msg: "Website returned HTTP {{ site_check.status }}, expected 200"


---

# Task 7 — Validate and Run the Ansible Playbook

## Goal

Validate the syntax of the multi-play Ansible playbook and run it to install Nginx, deploy the Mini Finance website, and verify the deployment.

### Evidence

#### Screenshot 10 — Successful playbook syntax check showing `playbook: site.yml`

![alt text](screenshots\sc10-T7-ass4-week9.png)
 
---

#### Screenshot 11 — Play 3 output showing the successful HTTP verification and assertion

![alt text](screenshots\sc11-T7-ass4-week9.png)

---

#### Screenshot 12 — Final `PLAY RECAP` showing `failed=0` and `unreachable=0`

![alt text](screenshots\sc11-T7-ass4-week9.png)

---

### Notes

This screenshot shows the final PLAY RECAP output after running the Ansible playbook to deploy the Mini Finance website on the AWS EC2 instance. The results confirm that the automation process completed successfully without any errors.

For the web server 98.88.216.4, Ansible completed 9 tasks successfully, with 4 tasks making changes to the server. The remaining tasks confirmed that the required configuration was already correct. The values failed=0 and unreachable=0 indicate that no task failed and Ansible maintained a successful connection to the remote server throughout the deployment.

For localhost, Ansible completed 3 tasks successfully. These included requesting the website through the server's public IP address and checking the HTTP response. The assertion task passed, confirming that the server returned HTTP status code 200, which indicates a successful HTTP request.

Overall, this final result verifies that Nginx is configured and running, the Mini Finance website has been deployed, the web directory ownership has been set to www-data, and the website is accessible through its public IP address. It demonstrates that the Ansible playbook automated the deployment and verified the website's availability successfully.

---

# Task 8 — Test the Mini Finance Website in a Browser

## Goal

Confirm that the Mini Finance website is publicly accessible through the Azure VM’s public IP address.

### Evidence

#### Screenshot 13 — Mini Finance website successfully loading in the browser, with the Azure VM’s public IP address visible in the address bar

![alt text](screenshots\sc13.1-T7-ass4-week9.png)

---

### Website URL

Add your deployed website URL below:

http://98.88.216.4/

---

# Task 9 — Create the Project README

## Goal

Create a `README.md` file to document the Mini Finance infrastructure and deployment project.

### Evidence

#### Screenshot 14 — Completed `README.md` displayed in the VS Code Markdown preview or terminal

![alt text](week-09-ansible\screenshots\sc14-T9-ass4-week9.png)

---

### README Content

Copy and paste the complete contents of your `README.md` file below:

# Mini Finance: Infrastructure Provisioning and Website Deployment with Terraform and Ansible

## Project Objective

This project provisions a small cloud network and Ubuntu virtual machines with Terraform, then configures them with Ansible. Ansible installs Nginx, clones the Mini Finance website from GitHub, copies the site into Nginx's web root, and checks that the site responds with HTTP status code 200.

The assignment demonstrates the two halves of infrastructure automation. Terraform describes and creates the infrastructure declaratively. Ansible describes and applies the server configuration idempotently. Running the playbook a second time should change nothing.

## Tools and Technologies

- **Terraform**: declares and provisions the cloud infrastructure (`terraform/`)
- **Cloud provider**: AWS, using the `hashicorp/aws` provider (see the note under Infrastructure Created)
- **Ansible**: configures the servers and runs the deployment (`ansible/`)
- **Nginx**: web server that serves the static website
- **Git**: clones the Mini Finance project onto the server
- **rsync**: copies the website files from `/opt/mini-finance/` to `/var/www/html/`

## Infrastructure Created

Terraform creates the following resources in the `us-east-1` region:

| Resource | Terraform type | Purpose |
|---|---|---|
| VPC (virtual network) | `aws_vpc` | Private network with CIDR `10.0.0.0/16` |
| Internet gateway | `aws_internet_gateway` | Gives the VPC access to the internet |
| Subnet | `aws_subnet` | Subnet with CIDR `10.0.1.0/24` in `us-east-1a` |
| Route table and association | `aws_route_table`, `aws_route_table_association` | Sends `0.0.0.0/0` traffic to the internet gateway |
| Security group | `aws_security_group` | Allows SSH (22) and HTTP (80) inbound, and all outbound traffic |
| SSH key pair | `aws_key_pair` | Public key registered for SSH login |
| Ubuntu 22.04 LTS virtual machines | `aws_instance` | One instance per role: `web1`, `web2`, `app1`, `db1` |
| Elastic IP addresses | `aws_eip` | Static public IPs for `web1` and `web2` |

The Ubuntu image is looked up at plan time as the most recent Canonical Ubuntu 22.04 AMI, so no AMI ID is hard-coded.

**Note on the cloud provider:** the assignment brief lists Microsoft Azure and Azure resource names (Resource Group, Network Interface). The Terraform code in this repository targets AWS, so the table above lists AWS resources. The Azure equivalents are: VPC → Virtual Network, security group → Network Security Group, Elastic IP → Public IP address, and the EC2 instance's network interface → Network Interface. AWS has no Resource Group resource.

## Ansible Deployment Workflow

The playbook `ansible/site.yml` contains three plays that run in order against the hosts in `ansible/inventory.ini`.

1. **Install and configure Nginx** (`web` group, `become: true`)
   - Updates the apt package cache.
   - Installs `nginx`, `git` and `rsync`.
   - Starts Nginx and enables it to start at boot.

2. **Clone and deploy the Mini Finance website** (`web` group, `become: true`)
   - Clones the Mini Finance repository into `/opt/mini-finance`.
   - Synchronizes the website files to `/var/www/html/` with `ansible.posix.synchronize`, excluding the `.git` directory.
   - Sets the owner and group of `/var/www/html/` to `www-data:www-data`.
   - Notifies a handler that reloads Nginx when the synchronization reports a change.

3. **Verify the deployment** (`localhost`, local connection, no privilege escalation)
   - Sends an HTTP GET request to the web server's public IP with `ansible.builtin.uri`.
   - Expects status code 200 and registers the result.
   - Uses `ansible.builtin.assert` to fail the run if `status` is not 200.

Run the playbook from the `ansible` directory:

```bash
ansible-playbook -i inventory.ini site.yml
```

Before the first run, check the syntax with `ansible-playbook -i inventory.ini site.yml --syntax-check`.

## Verification

Verification happens in two ways.

**With Ansible:** the third play sends an HTTP request from the controller to the web server and asserts that the response code is 200. A failed assertion stops the run and shows the actual status code, so a broken deployment is reported in the playbook output.

**With a web browser:** open `http://<web server public IP>/` in a browser, using the address from the Terraform output `public_ips`. The Mini Finance page should load. If it does, Nginx is serving the files from `/var/www/html/` and port 80 is open in the security group. If the page does not load, check the security group rules and the Nginx service status on the server.

## Challenge and Solution

I did not record a deployment error for this project, so the point below is a check that prevents deployment problems rather than a problem I solved.

**Checked: the inventory must match the Terraform output.** Ansible connects to whatever address is in `inventory.ini`. The Elastic IP is created by Terraform, so the address must be copied from the `public_ips` output after `terraform apply`. If the address is stale, the playbook fails at the first task and the verification play cannot reach the site. The SSH rule in the security group must also allow the address Ansible connects from, and the HTTP rule must allow port 80 for the browser check.

## What You Learned

Terraform and Ansible solve different parts of the same problem. Terraform answers "what infrastructure exists?" It creates the network, security rules, and virtual machines, and it keeps a state file that records them. Ansible answers "what should be running on those machines?" It installs packages, deploys files, and checks results on each host.

Using both tools together showed the value of splitting responsibilities. Terraform outputs such as public IPs feed into the Ansible inventory, so the two steps have to run in order. Ansible tasks such as `apt`, `service`, and `file` can be repeated safely, and the verification play gives a clear pass or fail result at the end of each deployment.


---

# LinkedIn Post Required

## Evidence

#### Screenshot 15 — Published LinkedIn post showing the text and at least one deployment screenshot

![alt text](week-09-ansible\screenshots\post-linked-in-ass4-week9.png)

---

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://lnkd.in/p/dTKq_BvQ

---

### LinkedIn Submission Notes

**One challenge you faced and how you fixed it:**

One challenge I faced was that the Ansible playbook got stuck while cloning the Mini Finance repository. After troubleshooting, I discovered that the GitHub repository URL was incorrect. I replaced it with the correct URL, tested the repository connection, and ran the playbook again. The deployment then completed successfully, and the HTTP verification returned status code 200.

---

**One real-world example where you can use this learning:**

This knowledge can be used in a real-world company to deploy and maintain websites on AWS automatically. Terraform can provision the cloud infrastructure, while Ansible can install software, configure web servers, deploy application files, and verify that the website is working. This approach saves time, reduces manual errors, and makes deployments more consistent.

---

# Assignment Questions

Answer the following in your own words:

**1. What did you provision using Terraform in this assignment?**

I used Terraform to provision AWS infrastructure, including EC2 instances, networking resources, security groups, and the required configurations to access the instances. Terraform also provided the public IP addresses of the instances.

---

**2. What did Ansible configure and deploy in this assignment?**

Ansible installed and configured Nginx on the EC2 web server, cloned the Mini Finance repository from GitHub, synchronized the website files to the web root directory, and configured the ownership for www-data. It also reloaded Nginx and verified that the website returned HTTP status code 200.

---

**3. Why is SSH access on port `22` restricted to your public IP address?**

Ansible installed and configured Nginx on the EC2 web server, cloned the Mini Finance repository from GitHub, synchronized the website files to the web root directory, and configured the ownership for www-data. It also reloaded Nginx and verified that the website returned HTTP status code 200.

---

**4. Why is HTTP port `80` open to the internet?**

HTTP port 80 is open to the internet because users need to access the website through a web browser. Allowing inbound traffic on this port makes the Mini Finance website publicly accessible through the EC2 instance's public IP address.

---

**5. What is the purpose of the Ansible inventory file?**

The Ansible inventory file defines the hosts that Ansible manages. It contains the EC2 instances' IP addresses and can specify connection details such as the SSH username and private key. It allows Ansible to know which servers should receive the configuration and deployment tasks.



---

**6. Why does the playbook use separate plays for install, deploy, and verify?**

Separate plays organize the automation into clear stages. The installation play prepares the server and installs Nginx, the deployment play copies the website files and configures the web root, and the verification play checks whether the website is accessible. This structure makes the playbook easier to understand, troubleshoot, and maintain.

---

**7. Why is `rsync` useful when deploying website files?**

rsync is useful because it synchronizes files between directories efficiently. It transfers only the files that need to be updated instead of copying everything unnecessarily. This can reduce deployment time and network traffic, especially when a website contains many files.

---

**8. What does the Ansible `uri` module verify in this assignment?**

The Ansible uri module sends an HTTP request to the website's public IP address and checks the response. In this assignment, it verifies that the website returns HTTP status code 200, indicating that the server successfully responds to the request. The assertion task then confirms that the expected status code was received.

---

**9. What issue did you face during this assignment, and how did you fix it?**

I initially encountered a problem when Ansible tried to clone the Mini Finance repository. The original GitHub URL was incorrect, which prevented the repository from being accessed. I found the correct repository URL, updated the playbook, and reran it. The repository was cloned successfully, the website files were deployed, and the final HTTP verification passed.

---

**10. What did you learn from using Terraform and Ansible together?**

I learned that Terraform and Ansible complement each other in cloud automation. Terraform provisions the infrastructure, such as EC2 instances and networking resources, while Ansible configures the servers and deploys applications. Using both tools together makes the deployment process more automated, repeatable, efficient, and easier to manage.

---

# Required Files

Confirm that the following files are included in your assignment folder:

- [ ] `.gitignore`
- [ ] `README.md`
- [ ] `terraform/providers.tf`
- [ ] `terraform/main.tf`
- [ ] `terraform/variables.tf`
- [ ] `terraform/outputs.tf`
- [ ] `ansible/inventory.ini`
- [ ] `ansible/site.yml`

---

# Submission Instructions

- Add all required screenshots in the correct order.
- Full Name must be visible in required screenshots.
- Add the Azure VM public IP address.
- Add the final Mini Finance website URL.
- Paste `inventory.ini`, `site.yml`, and `README.md` as editable text.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, passwords, Azure credentials, subscription IDs, Terraform state contents, or other sensitive information.

---

# Completion Checklist

- [ ] Task 1: `mini-finance` project structure created
- [ ] Task 1: `.gitignore` created
- [ ] Task 2: Terraform Azure infrastructure code created
- [ ] Task 2: `Allow-SSH` rule configured for port `22`
- [ ] Task 2: `Allow-HTTP` rule configured for port `80`
- [ ] Task 2: NSG associated with the Network Interface
- [ ] Task 3: `terraform fmt` completed
- [ ] Task 3: `terraform init` completed
- [ ] Task 3: `terraform validate` completed successfully
- [ ] Task 3: `terraform apply` completed successfully
- [ ] Task 3: `terraform output public_ip` displayed the VM public IP
- [ ] Task 4: Passwordless SSH works from the Ansible controller
- [ ] Task 5: `inventory.ini` created
- [ ] Task 5: Ansible ping returns `SUCCESS` and `pong`
- [ ] Task 6: `site.yml` contains three separate plays
- [ ] Task 6: Play 1 installs Nginx, Git, and rsync
- [ ] Task 6: Play 2 clones and deploys the Mini Finance website
- [ ] Task 6: Play 3 verifies HTTP status code `200`
- [ ] Task 7: Playbook syntax check passes
- [ ] Task 7: Ansible playbook completes successfully
- [ ] Task 7: Final recap shows `failed=0` and `unreachable=0`
- [ ] Task 8: Mini Finance website loads in the browser
- [ ] Task 8: Azure VM public IP is visible in the browser screenshot
- [ ] Task 9: `README.md` completed
- [ ] Screenshots 1–15 are included
- [ ] `inventory.ini`, `site.yml`, and `README.md` are pasted as editable text
- [ ] Assignment questions are answered
- [ ] LinkedIn post published with Anyone visibility
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