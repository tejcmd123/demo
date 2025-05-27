

```markdown
# File: ansible-notes/ansible-app-deployment.adoc

## Full Lab: Deploy Spring PetClinic with Docker on Alpine (Play with Docker) [cite: 1]
Author: MCA DevOps Workshop [cite: 1]

### 🎯 Objective

This lab guides you to: [cite: 1]
- Configure an Alpine Linux environment on Play with Docker (PWD) [cite: 1]
- Install Java 17, Maven, Git [cite: 1]
- Clone and build the Spring PetClinic app [cite: 1]
- Create a Dockerfile [cite: 1]
- Build and run the app in a Docker container [cite: 1]
- Automate the deployment using Ansible [cite: 1]

### 🧪 Environment

- Platform: https://labs.play-with-docker.com [cite: 1]
- Alpine Linux instances (2 nodes) [cite: 1]
- Internet access inside container (provided by PWD) [cite: 1]
- Docker is pre-installed in the PWD instance [cite: 1]

### 🐧 Step 1: Launch Alpine on Play with Docker

1. Visit https://labs.play-with-docker.com [cite: 2]
2. Start a session, then click `+ ADD NEW INSTANCE` twice to create two instances [cite: 2]
3. Choose *Alpine* as your image [cite: 3]
4. Identify one as the control node and one as the managed node [cite: 3]

### 🔧 Step 2: Install Required Tools (on Control Node) [cite: 3]

```bash
apk update
apk add git openjdk17 maven curl wget unzip bash ansible
```
 [cite: 3]

### ⚙️ Step 3: Prepare PetClinic App [cite: 3]

```bash
git clone [https://github.com/spring-projects/spring-petclinic.git](https://github.com/spring-projects/spring-petclinic.git)
cd spring-petclinic
mvn clean package -DskipTests
```
 [cite: 3]

### 📂 Step 4: Create Dockerfile [cite: 3]

```bash
cat <<EOF > Dockerfile
FROM openjdk:17
COPY spring-petclinic-3.4.0-SNAPSHOT.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
EOF
```
 [cite: 3]

### 🤖 Step 5: Create Ansible Inventory File [cite: 3]

```bash
echo '[web]
192.168.0.12 ansible_user=root ansible_ssh_common_args="-o StrictHostKeyChecking=no"' > inventory
```
 [cite: 3]

Replace `192.168.0.12` with your second node’s IP address. [cite: 3]

### 📜 Step 6: Create Ansible Playbook [cite: 4]

Create a file `deploy_petclinic.yml`: [cite: 4]

```yaml
- name: Deploy PetClinic on Node 2 using Docker
  hosts: web
  become: true
  tasks:
    - name: Ensure destination directory exists
      ansible.builtin.file:
        path: /root/petclinic
        state: directory

    - name: Copy JAR file
      ansible.builtin.copy:
        src: ./spring-petclinic/target/spring-petclinic-3.4.0-SNAPSHOT.jar
        dest: /root/petclinic/spring-petclinic-3.4.0-SNAPSHOT.jar

    - name: Copy Dockerfile
      ansible.builtin.copy: [cite: 5]
        src: ./spring-petclinic/Dockerfile [cite: 5]
        dest: /root/petclinic/Dockerfile [cite: 5]

    - name: Build Docker image
      ansible.builtin.shell:
        cmd: docker build -t petclinic-app . [cite: 5]
        chdir: /root/petclinic [cite: 6]

    - name: Run Docker container
      ansible.builtin.shell:
        cmd: docker run -d -p 8080:8080 --name petclinic petclinic-app
```
 [cite: 6]

### 🚀 Step 7: Run the Playbook [cite: 6]

```bash
ansible-playbook -i inventory deploy_petclinic.yml
```
 [cite: 6]

### ✅ Step 8: Test the Deployment [cite: 6]

Visit: [cite: 6]
```text
http://<node2-ip>:8080
```
 [cite: 6]

### 🧼 Cleanup [cite: 6]

```bash
docker rm -f petclinic
```
 [cite: 6]

### 📌 Summary [cite: 6]

You have now automated the deployment of a Spring Boot app using Ansible and Docker on remote Alpine Linux nodes in Play with Docker! [cite: 6]

---
# File: ansible-notes/ansible-basic-labs.adoc [cite: 7]

## 🛠️ Ansible Practical Lab for MCA Students [cite: 7]
Author: DevOps Workshop [cite: 7]

### 🎯 Objective [cite: 7]

This lab introduces the basics of Ansible automation. [cite: 7] By the end of this practical, MCA students will be able to: [cite: 8]

- Understand how Ansible works [cite: 8]
- Run ad-hoc commands [cite: 8]
- Create and execute simple playbooks [cite: 8]
- Manage inventory files and use core modules like `ping`, `copy`, `file`, and `package` [cite: 8]

### 🧩 Lab Setup [cite: 8]

You will need: [cite: 8]

- 1 Ansible controller node (e.g., `node1`) [cite: 8]
- 1 or more managed nodes (e.g., `node2`, `node3`) [cite: 8]
- All systems should be Linux (Alpine/Ubuntu/CentOS) [cite: 8]

**TIP:** Use https://labs.play-with-docker.com to spin up 2–3 Alpine instances. [cite: 8] SSH access is preconfigured. [cite: 9]

### 🔧 Step 1: Install Ansible on the Controller Node [cite: 9]

```bash
apk update
apk add --no-cache ansible openssh
```
 [cite: 9]

### 📁 Step 2: Create Inventory File [cite: 9]

```bash
echo "[web]" > hosts
echo "192.168.0.21 ansible_user=root" >> hosts
echo "192.168.0.22 ansible_user=root" >> hosts
```
 [cite: 9]

Replace the IPs with your managed node IPs. [cite: 9]

### ⚡ Lab 1: Ad-Hoc Commands [cite: 10]

#### 🔹 1.1 Test Connection Using Ping Module [cite: 10]

```bash
ansible -i hosts web -m ping
```
 [cite: 10]

#### 🔹 1.2 Check Uptime [cite: 10]

```bash
ansible -i hosts web -m shell -a "uptime"
```
 [cite: 10]

#### 🔹 1.3 Check Disk Usage [cite: 10]

```bash
ansible -i hosts web -m shell -a "df -h"
```
 [cite: 10]

#### 🔹 1.4 Create a File Remotely [cite: 10]

```bash
ansible -i hosts web -m file -a "path=/tmp/mca.txt state=touch"
```
 [cite: 10]

#### 🔹 1.5 Install Package (Optional) [cite: 10]

(Use only if the system supports `apt` or `yum`) [cite: 10]

```bash
ansible -i hosts web -m package -a "name=tree state=present"
```
 [cite: 10]

### 📜 Lab 2: Writing and Running a Playbook [cite: 10]

#### 🔹 2.1 Create the Playbook File [cite: 10]

Create a file named `mca_demo.yml`: [cite: 10]

```yaml
- name: MCA Ansible Playbook Demo
  hosts: web [cite: 11]
  tasks: [cite: 11]
    - name: Create a welcome file [cite: 11]
      copy: [cite: 11]
        content: "Welcome MCA Students to Ansible Lab!\n" [cite: 11]
        dest: /tmp/welcome.txt [cite: 11]

    - name: Ensure a directory exists [cite: 11]
      file: [cite: 11]
        path: /opt/mca-lab [cite: 11]
        state: directory [cite: 11]

    - name: Install tree (optional) [cite: 11]
      package: [cite: 11]
        name: tree [cite: 11]
        state: present [cite: 12]
```
 [cite: 12]

#### 🔹 2.2 Run the Playbook [cite: 12]

```bash
ansible-playbook -i hosts mca_demo.yml
```
 [cite: 12]

#### 🔹 2.3 Verify the Changes [cite: 12]

Log in to any managed node and run: [cite: 12]

```bash
cat /tmp/welcome.txt
ls -l /opt/mca-lab
tree /opt
```
 [cite: 12]

### 🧠 Viva / Review Questions [cite: 12]

1. What is the role of the inventory file in Ansible? [cite: 12]
2. What is an ad-hoc command? Give one example. [cite: 13]
3. How is a playbook different from an ad-hoc command? [cite: 13]
4. Name three commonly used Ansible modules. [cite: 14]
5. How can you ensure a directory exists on a remote system using Ansible? [cite: 14]

### ✅ Expected Outcomes [cite: 15]

After completing this lab, students will: [cite: 15]

- Understand how Ansible communicates over SSH [cite: 15]
- Use ad-hoc commands to perform basic automation [cite: 15]
- Create and run a simple playbook with common modules [cite: 15]
- Be confident using Ansible for small automation tasks [cite: 15]

### 📎 Additional Tips [cite: 15]

- Use `--check` with playbooks to run in dry-run mode [cite: 15]
- Use `-v` or `-vvv` for more verbose output [cite: 15]
- Learn more modules at https://docs.ansible.com/ [cite: 15]

🎉 Congratulations on completing your first Ansible lab! [cite: 15]

---
# File: ansible-notes/ansible-cheatsheet.adoc [cite: 16]

## 📘 Ansible Cheat Sheet [cite: 16]
Author: DevOps Workshop [cite: 16]

### 🔰 What is Ansible? [cite: 16]
Ansible is an open-source automation tool used for: [cite: 17]
- Configuration management [cite: 17]
- Application deployment [cite: 17]
- Task automation [cite: 17]

It uses **YAML** to define automation tasks (called *playbooks*) and works over **SSH**—no agent required. [cite: 17]

### 🧰 Basic Terminology [cite: 18]

| Term      | Meaning                                     |
|-----------|---------------------------------------------|
| Inventory | A list of managed hosts [cite: 18]                    |
| Module    | The units of work executed by Ansible [cite: 19]     |
| Ad-hoc    | One-line command for quick tasks [cite: 20]          |
| Playbook  | A YAML file describing a set of tasks [cite: 20]      |
| Role      | A structured way of organizing playbooks [cite: 21] |
| Facts     | Information collected about a system [cite: 22]    |

### 📂 Inventory File Example [cite: 22]

```ini
[web]
192.168.0.21 ansible_user=root

[db]
192.168.0.22 ansible_user=root
```
 [cite: 22]

### ⚡ Ad-Hoc Commands [cite: 22]

#### Ping all hosts [cite: 22]

```bash
ansible all -i hosts -m ping
```
 [cite: 22]

➡️ Uses the `ping` module to test SSH connectivity. [cite: 22]

#### Run shell command [cite: 23]

```bash
ansible all -i hosts -m shell -a "uptime"
```
 [cite: 23]

➡️ Runs a shell command on all inventory hosts. [cite: 23]

#### Create a file [cite: 24]

```bash
ansible web -i hosts -m file -a "path=/tmp/test.txt state=touch"
```
 [cite: 24]

➡️ Creates a new file on all `web` hosts. [cite: 24]

#### Install a package (if supported by system) [cite: 25]

```bash
ansible web -i hosts -m package -a "name=tree state=present"
```
 [cite: 25]

➡️ Installs the `tree` package if available. [cite: 25]

### 📜 Playbook Structure [cite: 26]

```yaml
- name: Example Playbook
  hosts: web
  become: true

  tasks:
    - name: Ensure nginx is installed
      package:
        name: nginx
        state: present

    - name: Start nginx service
      service:
        name: nginx
        state: started
```
 [cite: 26]

### 📦 Common Modules [cite: 26]
| Module       | Description                           |
|--------------|---------------------------------------------|
| `ping`       | Test connectivity [cite: 28]                    |
| `copy`       | Copy a file to remote host [cite: 29]           |
| `file`       | Create/delete files or directories [cite: 30]   |
| `package`    | Install or remove software packages [cite: 31]  |
| `service`    | Manage system services [cite: 32]               |
| `user`       | Manage user accounts [cite: 33]                 |
| `debug`      | Print variables/debug messages [cite: 34]     |
| `setup`      | Gather facts about the system [cite: 35]        |

### 🛠️ Useful Options [cite: 35]

| Option            | Purpose                             |
|-----------------|------------------------------------------|
| `-i hosts`        | Specify inventory file [cite: 39]             |
| `-m <module>`     | Use a specific module [cite: 40]              |
| `-a <args>`       | Pass arguments to module [cite: 41]           |
| `--check`         | Dry-run mode [cite: 43]                       |
| `-v / -vvv`       | Verbose output [cite: 45]                     |

### 📁 Example: Docker + Nginx Playbook [cite: 45]

```yaml
- name: Run Nginx in Docker
  hosts: web
  become: true
  collections:
    - community.docker

  tasks:
    - name: Pull Nginx image
      community.docker.docker_image:
        name: nginx
        source: pull

    - name: Start Nginx container
      community.docker.docker_container:
        name: nginx-server [cite: 46]
        image: nginx [cite: 46]
        state: started [cite: 46]
        ports: [cite: 46]
          - "8080:80" [cite: 46]
```
 [cite: 46]

### 🎯 Tips [cite: 46]

- Use `ansible-doc <module>` to get help on any module [cite: 46]
- Always test with `--check` before applying real changes [cite: 46]
- Use `tags` in playbooks to selectively run tasks [cite: 46]
- Use `handlers` for restarting services only when needed [cite: 46]

### 🔗 Resources [cite: 46]

- Ansible Docs: https://docs.ansible.com/ [cite: 46]
- Module Index: https://docs.ansible.com/ansible/latest/collections/ [cite: 46]

--- [cite: 46]

👏 That's it! [cite: 47] Use this cheat sheet during practicals or workshops for a quick reference! [cite: 47]

---
# File: ansible-notes/ansible-command-notes.adoc [cite: 48]

## 🧪 Ansible Command Breakdown & Explanations [cite: 48]

This section explains common Ansible CLI commands used in MCA practicals. [cite: 48]

### 📌 1. Basic Ping Test [cite: 49]

```bash
ansible all -i hosts -m ping
```
 [cite: 49]

| Part              | Meaning                                                  |
|-------------------|-------------------------------------------------------------------|
| `ansible`         | The Ansible command-line tool [cite: 51]                           |
| `all`             | Target all hosts from the inventory [cite: 52]                       |
| `-i hosts`        | Use the inventory file named `hosts` [cite: 53]                      |
| `-m ping`         | Use the `ping` module to check connectivity (via SSH) [cite: 55]   |

📝 The `ping` module doesn't use ICMP — it checks SSH + Python availability. [cite: 55]
--- [cite: 56]

### 📌 2. Run a Shell Command [cite: 56]

```bash
ansible web -i hosts -m shell -a "uptime"
```
 [cite: 56]

| Part              | Meaning                                                        |
|-------------------|-------------------------------------------------------------------------|
| `web`             | Target only the `web` group from the inventory [cite: 59]                |
| `-m shell`        | Use the `shell` module to run Linux shell commands [cite: 60]            |
| `-a "uptime"`     | Arguments to the shell module — runs the `uptime` command [cite: 61]     |

📝 You can replace `"uptime"` with any shell command like `df -h`, `ls -l`, etc. [cite: 61]

--- [cite: 61]

### 📌 3. Create a File on Remote Hosts [cite: 61]

```bash
ansible web -i hosts -m file -a "path=/tmp/test.txt state=touch"
```
 [cite: 61]

| Part             | Meaning                                                    |
|------------------|---------------------------------------------------------------------|
| `-m file`        | Use the `file` module [cite: 62]                                     |
| `path=...`       | The path where the file should be created [cite: 63]                 |
| `state=touch`    | Creates an empty file (like `touch` command) [cite: 64]              |

📝 Other `state` values: `absent` (delete), `directory` (make folder) [cite: 64]

--- [cite: 64]

### 📌 4. Install a Package (e.g., `tree`) [cite: 64]

```bash
ansible web -i hosts -m package -a "name=tree state=present"
```
 [cite: 64]

| Part            | Meaning                                                       |
|-----------------|------------------------------------------------------------------------|
| `-m package`     | Use the `package` module (works on many OS types) [cite: 65]          |
| `name=tree`      | Package to install [cite: 66]                                         |
| `state=present` | Ensures the package is installed [cite: 67]                           |

📝 If you're on Alpine Linux, `tree` can also be installed using `apk add tree`. [cite: 67]
--- [cite: 68]

### 📌 5. Run a Playbook [cite: 68]

```bash
ansible-playbook -i hosts nginx_docker.yml
```
 [cite: 68]

| Part                 | Meaning                                                                                                |
|----------------------|--------------------------------------------------------------------------------------------------------|
| `ansible-playbook`   | Command to run a YAML-based playbook [cite: 68]                                                                  |
| `-i hosts`           | Inventory file to use [cite: 69]                                                              |
| `nginx_docker.yml`   | Name of the playbook file to run [cite: 70]                                                             |

📝 Playbooks are used for complex and repeatable automation — much better than ad-hoc commands for real-world use. [cite: 70]
--- [cite: 71]

### 📌 6. Check Available Modules [cite: 71]

```bash
ansible-doc -l
```
 [cite: 71]

| Part            | Meaning                                         |
|-----------------|-------------------------------------------------|
| `ansible-doc`   | Shows documentation for Ansible modules [cite: 71]        |
| `-l`            | Lists all available modules [cite: 73]                   |

To view details of a specific module: [cite: 73]

```bash
ansible-doc file
```
 [cite: 73]

📝 Use this to learn module options without Googling everything. [cite: 73]
--- [cite: 74]

### 📝 Summary Table of Flags [cite: 74]

| Flag       | Description                               |
|-----------|----------------------------------------------------|
| `-i`       | Specify the inventory file [cite: 75]               |
| `-m`       | Specify the module to run [cite: 76]                |
| `-a`       | Provide arguments to the module [cite: 77]          |
| `--check` | Dry-run mode (show what would change) [cite: 78]      |
| `-v`/`-vvv` | Increase verbosity for debugging [cite: 78]                  |

--- [cite: 78]

This breakdown helps students **understand not just how to run commands, but also why** each part is needed. [cite: 79]

---
# File: ansible-notes/ansible-nginx-with-docker-module.adoc [cite: 79]

## 🚀 Ansible + Docker: Deploy Nginx (Play with Docker Lab) [cite: 79]
DevOps Workshop for MCA Students [cite: 79]

### 🎯 Objective [cite: 79]

Use Ansible to automate the deployment of an Nginx container using Docker on a remote node inside the Play with Docker (PWD) platform. [cite: 79]

### 🧱 Setup Environment on Play with Docker [cite: 80]

1. Go to https://labs.play-with-docker.com [cite: 80]
2. Click "Start" and add **two Alpine instances**: [cite: 80]
   - `node1`: Ansible Controller [cite: 80]
   - `node2`: Managed Node (Docker host) [cite: 80]
3. Copy the IP address of `node2` — you will use it in the inventory file. [cite: 80]

### ⚙️ Step 1: Install Ansible on Controller Node (node1) [cite: 81]

```bash
apk update
apk add --no-cache ansible py3-pip
python3 -m venv venv
source venv/bin/activate
pip install docker
ansible-galaxy collection install community.docker
```
 [cite: 81]

| Command                                               | Description                                                              |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `apk update`                                           | Update Alpine package index [cite: 82]                                             |
| `apk add ansible`                                      | Install Ansible [cite: 83]                                                       |
| `pip install docker`                                   | Install Python Docker SDK (required for Docker modules) [cite: 84]               |
| `ansible-galaxy collection install community.docker` | Install official Docker modules [cite: 85]                                         |

### 🗂️ Step 2: Create Inventory File [cite: 85]

```bash
echo "[web]" > hosts
echo "192.168.0.21 ansible_user=root" >> hosts
```
 [cite: 85]

| Command                | Description                                                               |
|------------------------|------------------------------------------------------------------------------------|
| `[web]`                  | Group of target machines [cite: 87]                                                 |
| `192.168.0.21`         | IP address of node2 (replace with your actual IP) [cite: 88]                        |
| `ansible_user=root`    | Ansible will SSH as the root user [cite: 89]                                        |

### 📜 Step 3: Create Ansible Playbook [cite: 89]

Create a file named `nginx_docker.yml`. [cite: 89]
```yaml
- name: Run Nginx on Docker using Ansible (PWD)
  hosts: web
  become: true
  collections:
    - community.docker

  tasks:
    - name: Pull Nginx image
      community.docker.docker_image:
        name: nginx
        source: pull

    - name: Run Nginx container
      community.docker.docker_container:
        name: nginx-server
        image: nginx
        state: started
        ports: [cite: 91]
          - "8080:80" [cite: 91]
```
 [cite: 91]

#### 🔍 Explanation [cite: 91]

| Command                                       | Description                                                                |
|---------------------------------------------|-------------------------------------------------------------------------------------|
| `hosts: web`                               | Targets all hosts in the `[web]` group [cite: 93]                                    |
| `become: true`                             | Uses sudo for root privileges [cite: 94]                                             |
| `docker_image` module                      | Pulls the nginx image if not present [cite: 95]                                      |
| `docker_container` module                  | Starts a container from the nginx image [cite: 96]                                   |
| `"8080:80"`                                  | Maps port 8080 on the host to 80 inside the container [cite: 97]                     |

### ▶️ Step 4: Run the Playbook [cite: 97]

```bash
ansible-playbook -i hosts nginx_docker.yml
```
 [cite: 97]

#### 🔍 Command Breakdown [cite: 97]

| Command                    | Description                                                     |
|--------------------------|------------------------------------------------------------------------------|
| `ansible-playbook`         | Command to run a YAML-based playbook [cite: 98]                               |
| `-i hosts`                 | Specifies the inventory file to use [cite: 100]                                 |
| `nginx_docker.yml`         | Playbook file to execute [cite: 101]                                          |

### ✅ Step 5: Test the Setup [cite: 101]

Open a browser and visit: [cite: 101]

```text
http://<node2-ip>:8080
```
 [cite: 101]

You should see the default **Nginx Welcome Page**. [cite: 101]

### 🧪 Bonus: Check Running Containers on Node2 [cite: 102]

SSH into node2 and run: [cite: 102]

```bash
docker ps
```
 [cite: 102]

You’ll see a running container named `nginx-server`. [cite: 102]

### 📝 Summary [cite: 103]

- You used **Ansible** to automate the Docker workflow. [cite: 103]
- Nginx is running inside a **Docker container** on a managed host. [cite: 104]
- All steps were tested on **Play with Docker (PWD)** using Alpine instances. [cite: 105]

### 🔗 Further Reading [cite: 106]

- Ansible Docs: https://docs.ansible.com/ [cite: 106]
- Docker Collection: https://docs.ansible.com/ansible/latest/collections/community/docker/ [cite: 106]
- Play with Docker: https://labs.play-with-docker.com [cite: 106]

---
# File: ansible-notes/ansible-nginx.adoc [cite: 106]

## Ansible Lab: Deploying Nginx using Docker (Play with Docker Compatible) [cite: 106]
Author: MCA Practical Lab Guide [cite: 106]

### Objective [cite: 106]

Learn how to use Ansible to deploy an Nginx container on a managed node using the `ansible.builtin.command` module. [cite: 107] This approach avoids Python Docker SDK dependencies and works well in minimal environments like Play with Docker. [cite: 107]

### Lab Environment [cite: 108]

- Platform: Play with Docker (https://labs.play-with-docker.com) [cite: 108]
- Nodes: [cite: 108]
  - node1: Ansible Controller [cite: 108]
  - node2: Managed Host [cite: 108]

### Step 1: Set Up the Inventory File [cite: 108]

On `node1`, create a file called `hosts` with the following content: [cite: 108]

```ini
[web]
192.168.0.12 ansible_user=root
```
 [cite: 108]

> **NOTE:** [cite: 108]
> Replace `192.168.0.12` with your actual `node2` IP address shown in the PWD terminal. [cite: 109]

### Step 2: Create the Ansible Playbook [cite: 109]

On `node1`, create a file called `nginx_docker.yml`: [cite: 109]

```yaml
- name: Run Nginx via Docker using shell command
  hosts: web
  become: true

  tasks:
    - name: Pull Nginx image
      ansible.builtin.command: docker pull nginx

    - name: Run Nginx container
      ansible.builtin.command: docker run -d -p 8080:80 --name nginx-server nginx
```
 [cite: 109]

#### 🔍 Playbook Breakdown [cite: 109]

| Line                                                       | Description                                                                                                                             |
|------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| `- name: Run Nginx...`                                     | Describes the playbook's purpose. [cite: 110]                                                                                                    |
| `hosts: web`                                               | Targets the `web` group defined in the inventory. [cite: 111]                                                                                    |
| `become: true`                                             | Elevates privilege to `root` to run Docker commands. [cite: 112]                                                                                 |
| `ansible.builtin.command`                                  | Ansible module to run shell commands directly on the managed node. [cite: 112]                                                                   |
| `docker pull nginx`                                        | Downloads the official Nginx image from Docker Hub to the managed node. [cite: 113]                                                              |
| `docker run -d -p 8080:80 --name nginx-server nginx` | Launches an Nginx container in detached mode, maps container port 80 to host port 8080, and names it `nginx-server`. [cite: 115] |

### Step 3: Run the Playbook [cite: 115]

From `node1`, execute: [cite: 115]

```bash
ansible-playbook -i hosts nginx_docker.yml
```
 [cite: 115]

This command tells Ansible to use the `hosts` file and execute the tasks in the `nginx_docker.yml` playbook. [cite: 116]

### Step 4: Verify the Deployment [cite: 116]

Open your browser and navigate to: [cite: 116]

```text
http://<node2-public-ip>:8080
```
 [cite: 116]

You should see the default Nginx welcome page. [cite: 117] Replace `<node2-public-ip>` with your actual IP. [cite: 117]

### Step 5: Clean Up (Optional) [cite: 117]

To stop and remove the container from `node2`: [cite: 117]

```bash
docker rm -f nginx-server
```
 [cite: 117]

### Summary of Key Commands and Their Meanings [cite: 117]

| Command                                                            | Explanation                                                                                                                |
|--------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| `ansible-playbook`                                                 | CLI tool to run Ansible playbooks. [cite: 118]                                                                                      |
| `-i hosts`                                                         | Specifies the inventory file to use. [cite: 118]                                                                                    |
| `become: true`                                               | Ensures tasks are run with elevated privileges. [cite: 119]                                                                         |
| `ansible.builtin.command`                                          | Runs shell-level commands on the target host. [cite: 120]                                                                           |
| `docker pull nginx`                                                | Pulls the latest Nginx image to the target system. [cite: 120]                                                                      |
| `docker run -d -p 8080:80 --name nginx-server nginx`      | Runs Nginx container in detached mode, maps ports, and assigns a name. [cite: 122]                                                    |
| `docker rm -f nginx-server`                                | Forcefully stops and deletes the container. [cite: 123]                                                                             |

### Troubleshooting Tips [cite: 123]

- If Docker commands fail, ensure Docker is installed and running on `node2`. [cite: 124]
- If playbook fails to connect, check the IP and SSH access. [cite: 125]
- If Nginx doesn't appear in the browser, verify the port mapping and firewall settings. [cite: 126]

---
# File: ansible-notes/ansible-QnA.adoc [cite: 126]

## Ansible Interview Questions and Answers [cite: 126]
:author: DevOps Faculty [cite: 126]
:doctype: article [cite: 126]
:toc: left [cite: 126]
:toclevels: 2 [cite: 126]
:icons: font [cite: 126]
:sectnums: [cite: 126]

### Basic Level [cite: 126]

#### 1. What is Ansible? [cite: 126]
Ansible is an open-source IT automation tool used for configuration management, application deployment, task automation, and multi-node orchestration. [cite: 127] It is agentless and uses SSH for communication. [cite: 128]

#### 2. What are the key features of Ansible? [cite: 128]
* Agentless architecture [cite: 129]
* Simple, YAML-based playbooks [cite: 129]
* Idempotent operations [cite: 129]
* Uses SSH for connectivity [cite: 129]
* Extensible with custom modules [cite: 129]

#### 3. What is an Ansible playbook? [cite: 129]
A playbook is a YAML file containing a list of tasks to be executed on managed nodes. [cite: 130] It defines what needs to be done on which hosts. [cite: 131]

#### 4. What is the difference between a playbook and an ad-hoc command? [cite: 132]
* Ad-hoc commands are used for quick, one-time tasks. [cite: 133]
* Playbooks are used for repeatable and consistent automation. [cite: 133]

#### 5. What is an inventory file? [cite: 134]

An inventory file is used to define the list of hosts or groups of hosts on which Ansible will operate. [cite: 134] It can be a static file or dynamically generated. [cite: 135]

### Intermediate Level [cite: 135]

#### 6. What are Ansible modules? [cite: 135]
Modules are small programs used by Ansible to perform specific tasks such as installing packages, copying files, or restarting services. [cite: 136] Examples: `yum`, `apt`, `copy`, `service`, `ping`. [cite: 137]

#### 7. What is a role in Ansible? [cite: 137]
Roles are a way to organize playbooks and related files into a structured format. [cite: 138] They contain folders like `tasks`, `handlers`, `templates`, `vars`, and `files`. [cite: 139]

#### 8. How do you run an Ansible playbook? [cite: 139]
Use the following command: [cite: 140]
```
ansible-playbook playbook.yml
```
 [cite: 140]

#### 9. What is Ansible Galaxy? [cite: 140]
Ansible Galaxy is a community hub where users can find, share, and reuse Ansible roles and collections. [cite: 141]

#### 10. How does Ansible ensure idempotency? [cite: 142]

Ansible modules are designed to be idempotent, meaning running the same playbook multiple times will not change the system after the first run unless necessary. [cite: 142]

### Advanced Level [cite: 143]

#### 11. How do you handle secrets in Ansible? [cite: 143]
Ansible provides a feature called *Vault* to encrypt secrets like passwords or keys. [cite: 144] Use the command: [cite: 145]
```
ansible-vault encrypt secrets.yml
```
 [cite: 145]

#### 12. How can you test Ansible roles? [cite: 145]
You can use tools like *Molecule* to test Ansible roles in different environments. [cite: 146]

#### 13. What is the purpose of the `become` directive? [cite: 147]
`become: yes` allows privilege escalation (e.g., sudo) to run tasks as a different user, typically root. [cite: 148]

#### 14. How can you execute a task only when a condition is met? [cite: 149]
Use the `when` clause: [cite: 150]
```yaml
- name: Install Apache only on CentOS
  yum:
    name: httpd
    state: present
  when: ansible_facts['os_family'] == "RedHat"
```
 [cite: 150]

#### 15. What is the difference between `copy` and `template` modules? [cite: 150]
* `copy` simply copies a file. [cite: 151]
* `template` processes a Jinja2 template before copying, allowing dynamic content. [cite: 151]

### Conclusion [cite: 152]

These questions cover fundamental to advanced concepts in Ansible. [cite: 153] Preparing these will help you in interviews and in practical Ansible usage. [cite: 153]

---
# File: docker-notes/docker-basic.adoc [cite: 154]

## 🐳 Docker Commands Explained in ASCII [cite: 154]
A Beginner-Friendly Docker Cheatsheet [cite: 154]

### 🚀 Basic Docker Terminology [cite: 154]

| Term        | Description                                                |
|-------------|------------------------------------------------------------|
| 🧱 IMAGE    | Read-only template to create containers (like OS) [cite: 155]   |
| 📦 CONTAINER | Running instance of an image [cite: 156]                            |
| 📚 VOLUME    | Persistent data storage that lives outside container [cite: 156]      |
| 🌐 NETWORK  | Communication layer between containers [cite: 157]                    |

### 🔧 `docker run` Flags and Options [cite: 157]

*General Syntax:* [cite: 157] 
`docker run [OPTIONS] IMAGE [COMMAND]` [cite: 157]

#### -d : Detached Mode [cite: 157]

*Description:* [cite: 157] 
Runs the container in the background. [cite: 158] You won’t see logs unless you check with: [cite: 158]

`docker logs <container_id>` [cite: 158]

*Example:* [cite: 158]
```
docker run -d nginx
```
 [cite: 158]

#### -p : Port Mapping [cite: 158]

*Syntax:* [cite: 158] 
`-p <hostPort>:<containerPort>` [cite: 158]

*Description:* [cite: 158] 
Maps a host port to a container port. [cite: 159] Required to access web services running inside containers. [cite: 159]

*Example:* [cite: 159]
```
docker run -p 8080:80 nginx
# Access Nginx via localhost:8080
```
 [cite: 159]

#### -v : Volume Mount [cite: 159]

*Syntax:* [cite: 159] 
`-v <volumeName>:<containerPath>` [cite: 159]

*Description:* [cite: 159] 
Mounts a volume for persistent storage that survives container deletion. [cite: 160]
*Example:* [cite: 160]
```
docker run -v mydata:/data busybox
```
 [cite: 160]

#### -it : Interactive + TTY [cite: 160]

*Description:* [cite: 160] 
Opens a shell inside the container so you can interact like a terminal. [cite: 161]
*Example:* [cite: 161]
```
docker run -it ubuntu bash
```
 [cite: 161]

### 📦 docker build [cite: 161]

*Description:* [cite: 161] 
Builds a custom image from a Dockerfile. [cite: 162]
*Syntax:* [cite: 162]
```
docker build -t <image-name> .
```
 [cite: 162]

### 📜 docker ps [cite: 162]

*Description:* [cite: 162] 
Lists running containers. [cite: 163]
*Add `-a` to show all containers (stopped + running):* [cite: 163]
```
docker ps -a
```
 [cite: 163]

### 👏 docker stop [cite: 163]

*Description:* [cite: 163] 
Stops a running container gracefully. [cite: 164]
*Syntax:* [cite: 164]
```
docker stop <container>
```
 [cite: 164]

### 🗑 docker rm [cite: 164]

*Description:* [cite: 164] 
Removes a stopped container. [cite: 165]
*Syntax:* [cite: 165]
```
docker rm <container>
```
 [cite: 165]

### 🧽 docker system prune [cite: 165]

*Description:* [cite: 165] 
Removes unused containers, images, volumes, and networks. [cite: 166]
*Syntax:* [cite: 166]
```
docker system prune -f
```
 [cite: 166]

### 📝 BONUS TIP: Naming Containers [cite: 166]

Use `--name` to give a friendly name to a container. [cite: 167]
*Example:* [cite: 167]
```
docker run --name myweb -d -p 8080:80 nginx
```
 [cite: 167]

### 🧠 REMEMBER [cite: 167]

- **Images** = Blueprints (read-only) [cite: 167]
- **Containers** = Live instances of images [cite: 167]
- **Volumes** = Persistent storage [cite: 167]
- **Networks** = Container communication [cite: 167]

### ✅ YOU NOW KNOW [cite: 167]

- What detached mode is [cite: 167]
- What port mapping does [cite: 167]
- How to attach volumes [cite: 167]
- How to run, build, and manage containers [cite: 167]

### 🧾 Practice Tip [cite: 167]

Use `docker --help` or `docker run --help` to explore all options! [cite: 168]

---
# File: docker-notes/docker-cheatsheet.adoc [cite: 168]

## 🚀 Basic Docker Commands [cite: 168]

### 📦 Image Commands [cite: 168]

| Command                      | Description                                  |
|------------------------------|----------------------------------------------|
| `docker pull <image>`        | Download image from Docker Hub [cite: 169]          |
| `docker images`              | List all downloaded images [cite: 169]                  |
| `docker rmi <image>`         | Remove image by name or ID [cite: 170]                |
| `docker build -t <name> .`   | Build image from Dockerfile [cite: 170]                 |

### 📦 Container Commands [cite: 170]

| Command                            | Description                                |
|-----------------------------------|----------------------------------------------|
| `docker run <image>`              | Run container from image [cite: 171]                    |
| `docker run -it <image> bash`     | Run interactively with bash [cite: 172]         |
| `docker run -d -p 8080:80 <image>`| Run detached with port mapping [cite: 172]            |
| `docker ps`                       | List running containers [cite: 173]                   |
| `docker ps -a`                    | List all containers [cite: 173]                         |
| `docker stop <container>`         | Stop a container [cite: 173]                            |
| `docker start <container>`        | Start a stopped container [cite: 174]                 |
| `docker restart <container>`      | Restart a container [cite: 174]                         |
| `docker rm <container>`           | Remove a container [cite: 175]                        |

### 🌐 Volume & Network [cite: 175]

| Command                             | Description                                  |
|-------------------------------------|----------------------------------------------|
| `docker volume create <name>`       | Create a Docker volume [cite: 175]                      |
| `docker volume ls`                | List volumes [cite: 176]                                |
| `docker network ls`                 | List Docker networks [cite: 176]                        |
| `docker network create <name>`      | Create a custom network [cite: 177]                   |
| `docker run --network <name> ...`   | Attach container to a network [cite: 177]               |

### ⚙️ Execution & Logging [cite: 177]

| Command                               | Description                                |
|--------------------------------------|----------------------------------------------|
| `docker exec -it <container> bash`   | Enter shell inside running container [cite: 178]        |
| `docker logs <container>`            | Show container logs [cite: 179]                         |
| `docker logs -f <container>`         | Follow logs live [cite: 179]                            |

### 📄 Dockerfile Related [cite: 179]

| Command                           | Description                                  |
|----------------------------------|----------------------------------------------|
| `docker build -t <name> .`     | Build from Dockerfile in current directory [cite: 180] |
| `docker tag <image> <repo:tag>`  | Tag image for pushing [cite: 181]                     |
| `docker push <repo:tag>`         | Push image to a registry [cite: 181]                    |

### ♻️ System Maintenance [cite: 181]

| Command                       | Description                                          |
|------------------------------|----------------------------------------------------------------|
| `docker system prune`        | Clean up unused images, containers, networks, volumes [cite: 182]   |
| `docker image prune`         | Remove unused images [cite: 182]                                          |
| `docker container prune`     | Remove stopped containers [cite: 183]                               |
| `docker volume prune`        | Remove unused volumes [cite: 183]                                         |

### 📃 Misc Utilities [cite: 183]

| Command                                | Description                                  |
|---------------------------------------|----------------------------------------------|
| `docker inspect <container\|image>` | Show detailed info in JSON format [cite: 184]       |
| `docker stats`                        | Live resource usage of running containers [cite: 184]   |
| `docker cp <container>:/file .`     | Copy file from container to host [cite: 185]            |

---
# File: docker-notes/docker-home-work.adoc [cite: 185]

## 🧪 Docker Practical: Node.js App Containerization [cite: 185]

🎯 **Objective**: Containerize a basic Node.js web server using Docker and perform core Docker operations like build, run, exec, logs, copy, stop, remove. [cite: 186]

### 📁 Project Structure [cite: 186]

```
docker-node-app/
├── app.js
└── Dockerfile
```
 [cite: 186]

### 📄 app.js [cite: 186]

```javascript
const http = require('http'); [cite: 186]
const PORT = 3000; [cite: 187]
http.createServer((req, res) => { [cite: 187]
  res.writeHead(200, { 'Content-Type': 'text/plain' }); [cite: 187]
  res.end('Hello from Dockerized Node.js App!\n'); [cite: 187]
}).listen(PORT, () => { [cite: 187]
  console.log(`Server running at http://localhost:${PORT}`); [cite: 188]
});
```
 [cite: 188]

### 📄 Dockerfile [cite: 188]

```dockerfile
FROM node:18

WORKDIR /usr/src/app
COPY app.js .

EXPOSE 3000
CMD ["node", "app.js"]
```
 [cite: 188]

### 🧪 Docker Commands [cite: 188]

| Command                                                              | Description                                                    |
|----------------------------------------------------------------------|----------------------------------------------------------------|
| `docker build -t node-docker-app .`                                | Build Docker image from Dockerfile [cite: 189]                          |
| `docker run -d -p 3000:3000 --name my-node-app node-docker-app`      | Run container in detached mode [cite: 190]                              |
| `docker ps`                                                          | List running containers [cite: 190]                                       |
| `docker logs my-node-app`                                            | View logs of the container [cite: 190]                                    |
| `docker exec -it my-node-app bash`                                 | Access container shell [cite: 191]                                      |
| `docker cp my-node-app:/usr/src/app/app.js ./copied-app.js`          | Copy file from container to host [cite: 191]                              |
| `docker stop my-node-app`                                          | Stop the container [cite: 192]                                          |
| `docker rm my-node-app`                                              | Remove the container [cite: 192]                                          |
| `docker rmi node-docker-app`                                       | Remove the image [cite: 193]                                            |
| `docker system prune -f`                                             | Clean up all unused Docker objects [cite: 193]                            |

### 🌐 Test Your App [cite: 193]

Open your browser and go to: `http://localhost:3000` [cite: 193]

Expected Output: [cite: 193]
```
Hello from Dockerized Node.js App! [cite: 194]
```
 [cite: 194]

🎉 **You've containerized a Node.js app and practiced essential Docker commands!** [cite: 194]

---
# File: docker-notes/docker-optimised-app-deployment.adoc [cite: 194]

## Full Lab: Deploy Spring PetClinic with Docker on Alpine (Play with Docker) [cite: 194]
Author: MCA DevOps Workshop [cite: 194]

### 🎯 Objective [cite: 194]

This lab guides you to: [cite: 194]
- Configure an Alpine Linux environment on Play with Docker (PWD) [cite: 194]
- Install Java 17, Maven, Git [cite: 194]
- Clone and build the Spring PetClinic app [cite: 194]
- Create a Dockerfile [cite: 194]
- Build and run the app in a Docker container [cite: 194]

### 🧪 Environment [cite: 194]

- Platform: https://labs.play-with-docker.com [cite: 194]
- Alpine Linux instance (from PWD) [cite: 194]
- Internet access inside container (provided by PWD) [cite: 194]
- Docker is pre-installed in the PWD instance [cite: 194]

### 🐧 Step 1: Launch Alpine on Play with Docker [cite: 195]

1. Visit https://labs.play-with-docker.com [cite: 195]
2. Start a session, then click `+ ADD NEW INSTANCE` [cite: 195]
3. Choose *Alpine* as your image [cite: 195]
4. Open the Alpine terminal [cite: 196]

### 🔧 Step 2: Install Required Tools [cite: 196]

```bash
apk update
apk add git openjdk17 maven curl wget unzip bash
```
 [cite: 196]

#### Explanation [cite: 196]

| Command     | Description                                   |
|---------------|---------------------------------------------------------|
| apk update    | Updates the Alpine package list [cite: 197]                        |
| apk add ...   | Installs Git, Java 17, Maven, Curl, etc. [cite: 198]           |

### ✅ Step 3: Verify Installations [cite: 198]

```bash
java -version
mvn -version
git --version
```
 [cite: 198]

### 🧬 Step 4: Clone the Spring PetClinic Repo [cite: 198]

```bash
git clone [https://github.com/spring-projects/spring-petclinic.git](https://github.com/spring-projects/spring-petclinic.git)
cd spring-petclinic
```
 [cite: 198]

### ⚙️ Step 5: Build the Application [cite: 198]

Use Maven to compile the source and package it: [cite: 198]

```bash
mvn clean package -DskipTests
```
 [cite: 198]

#### Explanation [cite: 198]

| Command                 | Description                                   |
|---------------------------|---------------------------------------------------------|
| mvn clean                 | Clears previously compiled files [cite: 199]                       |
| mvn package -DskipTests   | Builds a JAR file, skipping unit tests [cite: 200]               |

### 📂 Step 6: Verify the JAR File [cite: 200]

```bash
ls target/
```
 [cite: 200]

You should see a file like: [cite: 200]

```
spring-petclinic-3.1.0-SNAPSHOT.jar
```
 [cite: 200]

### 📦 Step 7: Create a Dockerfile [cite: 200]

Still inside the `spring-petclinic` directory, create a new file called `Dockerfile`: [cite: 200]

```dockerfile
FROM openjdk:17
COPY target/spring-petclinic-3.1.0-SNAPSHOT.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```
 [cite: 200]

#### Dockerfile Explained [cite: 200]

| Line                        | Description                                   |
|---------------------------|---------------------------------------------------------|
| FROM openjdk:17           | Base image with Java 17 runtime [cite: 201]                        |
| COPY target/... app.jar   | Copies the built JAR into the container [cite: 202]              |
| EXPOSE 8080               | Exposes port 8080 for external access [cite: 202]                  |
| CMD [...]                 | Starts the application using Java [cite: 203]                      |

### 🏗️ Step 8: Build the Docker Image [cite: 203]

```bash
docker build -t petclinic-app .
```
 [cite: 203]

#### Explanation [cite: 203]

| Option           | Description                                                                 |
|--------------------|---------------------------------------------------------------------------------------|
| -t petclinic-app   | Tags the image as "petclinic-app" [cite: 204]                                          |
| .                  | Specifies current directory as build context (must contain Dockerfile) [cite: 205]   |

### 🚀 Step 9: Run the PetClinic Container [cite: 205]

```bash
docker run -d -p 8080:8080 --name petclinic petclinic-app
```
 [cite: 205]

#### Explanation [cite: 205]

| Option           | Description                                                    |
|--------------------|--------------------------------------------------------------------------|
| -d                 | Detached mode (runs in background) [cite: 206]                                  |
| -p 8080:8080       | Maps host port 8080 to container port 8080 [cite: 207]                          |
| --name petclinic   | Assigns a name to the container [cite: 207]                                         |
| petclinic-app      | Name of the Docker image to run [cite: 208]                                     |

### 🔍 Step 10: Test It [cite: 208]

In your browser, open: [cite: 208]

```
http://<node-public-ip>:8080
```
 [cite: 208]

Use the public IP provided by Play with Docker for your Alpine node. [cite: 209]

### 🧼 Cleanup (Optional) [cite: 209]

```bash
docker rm -f petclinic
```
 [cite: 209]

### 📌 Summary [cite: 209]

You have successfully: [cite: 209]
- Set up Alpine with Java, Maven, Git [cite: 209]
- Cloned and built the Spring PetClinic app [cite: 209]
- Packaged it using a Dockerfile [cite: 209]
- Deployed and verified the app via Docker [cite: 209]

### 🚀 Bonus: Automate This with Ansible? [cite: 209]
Check out the follow-up lab to automate this using Ansible to: [cite: 210]
- Build the app on one node [cite: 210]
- Transfer to a second node [cite: 210]
- Deploy with Docker remotely [cite: 210]

---
# File: docker-notes/docker-practical.adoc [cite: 210]

## 🐳 Docker Practical Lab – Step-by-Step [cite: 210]

**Basic Commands | Web Server | Volumes | Dockerfile Build** [cite: 211]

📘 *Instructions*: Follow each step, read the explanation, and type the commands to learn how Docker works from scratch. [cite: 211] Let's begin! [cite: 212]

### 1️⃣ Run Hello-World Container [cite: 212]

❓ *Command to verify Docker is working?* [cite: 212]

```sh
docker run hello-world
```
 [cite: 212]

💬 *Explanation*: Downloads and runs a test container that prints a success message. [cite: 213]

### 2️⃣ List Containers [cite: 213]

❓ *How to list running containers?* [cite: 213]

```sh
docker ps
```
 [cite: 213]

❓ *How to list all (stopped + running) containers?* [cite: 213]

```sh
docker ps -a
```
 [cite: 213]

💬 *Explanation*: `ps` shows live containers; [cite: 213] `ps -a` shows history of all. [cite: 214]

### 3️⃣ List Downloaded Images [cite: 214]

❓ *How to see all locally available Docker images?* [cite: 214]

```sh
docker images
```
 [cite: 214]

💬 *Explanation*: Shows all pulled or built images on your system. [cite: 215]

### 4️⃣ Run NGINX in Detached Mode [cite: 215]

❓ *How to run Nginx web server in background (port 8080)?* [cite: 215]

```sh
docker run -d -p 8080:80 nginx
```
 [cite: 215]

💡 *Flags*: [cite: 215]
- `-d` → Detached (background mode) [cite: 215]
- `-p 8080:80` → Maps localhost:8080 to container:80 [cite: 215]

🧪 Visit: `http://localhost:8080` [cite: 215]

### 5️⃣ Use a Volume with BusyBox [cite: 215]

❓ *Create a volume named `mydata`:* [cite: 215]

```sh
docker volume create mydata
```
 [cite: 215]

❓ *Run BusyBox container with volume attached:* [cite: 215]

```sh
docker run -it -v mydata:/data busybox
```
 [cite: 215]

📝 *Inside Container:* [cite: 215]

```sh
echo "Persistent" > /data/file.txt
```
 [cite: 215]

💬 *Explanation*: Data saved in `/data` inside container persists across restarts. [cite: 216]

### 6️⃣ Create a Dockerfile for a Static Website [cite: 216]

📁 *Directory Structure:* [cite: 216]

```
myapp/
├── Dockerfile
└── site/
    └── index.html
```
 [cite: 216]

📄 *Dockerfile Content:* [cite: 216]

```dockerfile
FROM nginx:alpine
COPY ./site /usr/share/nginx/html
```
 [cite: 216]

💬 *Explanation*: This creates a new image that serves your custom HTML via Nginx. [cite: 217]

### 7️⃣ Build and Run Your Web App Image [cite: 217]

❓ *Build Docker image with name `mywebapp`:* [cite: 217]

```sh
docker build -t mywebapp . [cite: 218]
```
 [cite: 218]

❓ *Run the container on port 8081:* [cite: 218]

```sh
docker run -d -p 8081:80 mywebapp
```
 [cite: 218]

🧪 Visit in browser: `http://localhost:8081` [cite: 218]

💬 *Explanation*: You’ve built a Docker image with your own website inside it! [cite: 219]

### 🎉 Congratulations! [cite: 219]

You've practiced basic Docker skills: [cite: 219]

✔ Running containers [cite: 219] 
✔ Using volumes [cite: 219] 
✔ Building images [cite: 219] 
✔ Serving web apps [cite: 219]

### 🧹 Bonus Clean-Up Commands [cite: 219]

```sh
docker stop <container_id>
docker rm <container_id>
docker rmi <image_id>
docker volume rm mydata
docker system prune -f
```
 [cite: 219]

---
# File: docker-notes/Dockerfile [cite: 219]

```docker
FROM ubuntu:22.04 [cite: 219]

ENV DEBIAN_FRONTEND=noninteractive [cite: 219]

# Install Java and Maven [cite: 219]
RUN apt-get update && \ [cite: 219]
    apt-get install -y git curl unzip wget software-properties-common && \ [cite: 219]
    apt-get install -y openjdk-17-jdk && \ [cite: 219]
    wget [https://downloads.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz](https://downloads.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz) && \ [cite: 219]
    tar xzf apache-maven-3.9.6-bin.tar.gz -C /opt && \ [cite: 219]
    ln -s /opt/apache-maven-3.9.6 /opt/maven && \ [cite: 219]
    ln -s /opt/maven/bin/mvn /usr/bin/mvn && \ [cite: 220]
    rm apache-maven-3.9.6-bin.tar.gz [cite: 220]

ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64 [cite: 220]
ENV MAVEN_HOME=/opt/maven [cite: 220]
ENV PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin [cite: 220]

WORKDIR /app [cite: 220]

# Clone your actual Spring Boot app (replace with your Git repo) [cite: 220]
RUN git clone [https://github.com/spring-projects/spring-petclinic.git](https://github.com/spring-projects/spring-petclinic.git) . [cite: 220]
# Build and rename the jar [cite: 221]
RUN mvn clean package -DskipTests && \ [cite: 221]
    cp target/*.jar app.jar [cite: 221]

EXPOSE 8080 [cite: 221]

# Final fix: no wildcard in CMD [cite: 221]
CMD ["java", "-jar", "app.jar"] [cite: 221]
```
 [cite: 221]

---
# File: jenkins-notes/jenkins-groovy.adoc [cite: 221]

## Jenkins CI/CD Lab on Play with Docker (Single Instance) [cite: 221]
:author: DevOps Lab [cite: 221]
:revdate: 2025-04-07 [cite: 221]
:icons: font [cite: 221]
:toc: left [cite: 221]
:toclevels: 2 [cite: 221]

### Objective [cite: 221]

In this lab, you'll learn how to: [cite: 221]

- Set up Jenkins in a Docker container [cite: 221]
- Pull source code from GitHub [cite: 221]
- Build a Docker image for a Flask app [cite: 221]
- Run the app container (deploy it) [cite: 221]
- All within a single instance using Play with Docker [cite: 221]

### Prerequisites [cite: 221]

- Docker Hub account (for Play with Docker) [cite: 221]
- GitHub account [cite: 221]
- Basic understanding of Git, Docker, and Jenkins [cite: 222]

### Step 1: Start Jenkins on Play with Docker [cite: 222]

Go to https://labs.play-with-docker.com/ and click **Start**. [cite: 222] Once in, create a new instance. [cite: 223]

Run the following commands: [cite: 223]

```bash
docker volume create jenkins-data

docker run -d \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins-data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name jenkins \
  jenkins/jenkins:lts
```
 [cite: 223]

Open port `8080` and wait for Jenkins to start. [cite: 223] Unlock Jenkins: [cite: 224]

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```
 [cite: 224]

### Step 2: Configure Jenkins [cite: 224]

1. Open Jenkins in the browser (port 8080). [cite: 224]
2. Paste the initial admin password. [cite: 225]
3. Install **Suggested Plugins**. [cite: 225]
4. Create your admin user and proceed to the dashboard. [cite: 225]

### Step 3: Install Required Plugins [cite: 226]

From **Manage Jenkins > Plugins**, install: [cite: 226]

- Git [cite: 226]
- Pipeline [cite: 226]
- Docker Pipeline [cite: 226]

### Step 4: Use a Sample Flask App from GitHub [cite: 226]

Use this ready-made Flask app repo: [cite: 226]

GitHub: https://github.com/bobbyiliev/python-flask-app [cite: 226]

Project structure: [cite: 226]

```bash
. [cite: 227]
├── app.py [cite: 227]
├── requirements.txt [cite: 227]
└── Dockerfile [cite: 227]
```
 [cite: 227]

Example `Dockerfile`: [cite: 227]

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . . [cite: 228]
CMD ["python", "app.py"] [cite: 228]
```
 [cite: 228]

### Step 5: Create Jenkins Pipeline Project [cite: 228]

1. Go to Jenkins Dashboard → New Item [cite: 228]
2. Name it `Flask-CI-CD`, select **Pipeline** [cite: 229]
3. Scroll to the **Pipeline** section, and paste the following: [cite: 229]

```groovy
pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git '[https://github.com/bobbyiliev/python-flask-app.git](https://github.com/bobbyiliev/python-flask-app.git)'
            }
        }

        stage('Build Docker Image') { [cite: 230]
            steps { [cite: 230]
                script { [cite: 230]
                    dockerImage = docker.build("flask-app:latest") [cite: 230]
                } [cite: 230]
            } [cite: 230]
        } [cite: 230]

        stage('Run Container') { [cite: 231]
            steps { [cite: 231]
                script { [cite: 231]
                    sh 'docker rm -f flask-app || [cite: 231] true' [cite: 232]
                    dockerImage.run('-d -p 5000:5000 --name flask-app') [cite: 232]
                } [cite: 232]
            } [cite: 232]
        } [cite: 232]
    } [cite: 232]
} [cite: 232]
```

### Step 6: Run and Verify [cite: 232]

1. Click **Build Now** to start the pipeline. [cite: 233]
2. After success, open **port 5000**. [cite: 233]
3. You should see the Flask app message: _"Hello from Jenkins + Docker!"_ [cite: 234]

### Bonus [cite: 234]

- Add a GitHub webhook for automatic deploy on push. [cite: 235]
- Add a test stage or health check. [cite: 235]
- Try deploying using `docker-compose`. [cite: 236]

### Cleanup [cite: 236]

```bash
docker stop jenkins
docker rm jenkins
docker volume rm jenkins-data
docker rm -f flask-app
```
 [cite: 236]

### Summary [cite: 236]

You’ve set up a complete CI/CD pipeline using Jenkins in Docker, all inside a single container host on Play with Docker. [cite: 237] This simulates real-world CI/CD workflows in a lightweight, easy-to-replicate environment. [cite: 237]

---
# File: jenkins-notes/lab1/jenkins-script.groovy [cite: 238]

```groovy
pipeline {
    agent any
    environment {
        IMAGE_NAME = 'petclinic-app'
        CONTAINER_NAME = 'petclinic'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: '[https://github.com/PushkarrajPujari/spring-petclinic.git](https://github.com/PushkarrajPujari/spring-petclinic.git)'
            }
        } [cite: 239]
        stage('Build with Maven') { [cite: 239]
            steps { [cite: 239]
                sh 'mvn clean package -DskipTests' [cite: 239]
            } [cite: 239]
        } [cite: 239]
        stage('Build Docker Image') { [cite: 239]
            steps { [cite: 239]
                sh 'docker build -t $IMAGE_NAME .' [cite: 240]
            } [cite: 241]
        } [cite: 241]
        stage('Run Docker Container') { [cite: 241]
            steps { [cite: 241]
                sh '''
                docker rm -f $CONTAINER_NAME || [cite: 241] true [cite: 242]
                docker run -d -p 9090:8080 --name $CONTAINER_NAME $IMAGE_NAME [cite: 242]
                ''' [cite: 242]
            } [cite: 242]
        } [cite: 242]
    } [cite: 242]
} [cite: 242]
```
 [cite: 242]

---
# File: jenkins-notes/lab2/jenkins-ansible.adoc [cite: 242]

## Spring PetClinic Deployment using Jenkins, Ansible, and Docker [cite: 242]
:author: DevOps Workshop [cite: 242]
:revdate: 2025-04-07 [cite: 242]
:icons: font [cite: 242]

This guide walks through the process of automating the deployment of the Spring PetClinic application using Jenkins, Ansible, and Docker on a Play with Docker (PWD) Alpine environment. [cite: 243]

### 🛠️ Environment Setup on Jenkins Host [cite: 243]

Since Play with Docker (PWD) nodes come preinstalled with Docker, we did **not** install Docker manually. [cite: 244] We prepared the Alpine environment for Jenkins and Ansible with the following script: [cite: 244]

```sh
#!/bin/sh

echo "Updating package index..."
apk update

echo "Installing required packages: git, OpenJDK 17, Maven, curl, wget, unzip, bash..."
apk add git openjdk17 maven curl wget unzip bash

echo "Downloading latest stable Jenkins WAR file..."
wget [http://mirrors.jenkins.io/war-stable/latest/jenkins.war](http://mirrors.jenkins.io/war-stable/latest/jenkins.war)

echo "Installing font packages required for Jenkins UI..."
apk add --no-cache fontconfig ttf-dejavu

echo "Starting Jenkins in background with logs written to jenkins.log..."
nohup java -jar jenkins.war --httpPort=8080 > jenkins.log 2>&1 &

echo "Jenkins is starting... Tail the log with: tail -f jenkins.log"
```
 [cite: 244]

Additionally, we installed the following tools for deployment and automation: [cite: 244]

```sh
apk add ansible openssh python3
```
 [cite: 244]

### 📁 Ansible Configuration [cite: 244]

An Ansible folder was created at the root of the Jenkins node: [cite: 245]

```sh
mkdir /root/ansible
cd /root/ansible
```
 [cite: 245]

#### Inventory File: `inventory.ini` [cite: 245]

```ini
[remote]
remote-node ansible_host=192.168.0.13 ansible_user=root ansible_python_interpreter=/usr/bin/python3 ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```
 [cite: 245]

#### Playbook: `deploy-petclinic-with-jar.yml` [cite: 245]

```yaml

- name: Deploy PetClinic to remote node
  hosts: remote
  become: true
  tasks:
    - name: Create deployment directory on remote
      file:
        path: /root/petclinic-deploy/target
        state: directory

    - name: Copy built JAR to remote
      copy:
        src: /root/.jenkins/workspace/spring-app-ansible/spring-petclinic/target/spring-petclinic-3.4.0-SNAPSHOT.jar
        dest: /root/petclinic-deploy/target/spring-petclinic-3.4.0-SNAPSHOT.jar [cite: 246]

    - name: Copy Dockerfile from project to remote [cite: 246]
      copy: [cite: 246]
        src: /root/.jenkins/workspace/spring-app-ansible/spring-petclinic/Dockerfile [cite: 246]
        dest: /root/petclinic-deploy/Dockerfile [cite: 246]

    - name: Build Docker image on remote [cite: 246]
      shell: | [cite: 246]
        cd /root/petclinic-deploy [cite: 247]
        docker build -t petclinic-app . [cite: 247]
    - name: Run Docker container on remote [cite: 248]
      shell: | [cite: 248]
        docker rm -f petclinic || [cite: 248] true [cite: 249]
        docker run -d -p 8080:8080 --name petclinic petclinic-app [cite: 249]
```
 [cite: 249]

### 🤖 Jenkins Pipeline [cite: 249]

The Jenkins pipeline is configured to: [cite: 249]
1. Clone the Spring PetClinic repo [cite: 249]
2. Build the JAR with Maven [cite: 249]
3. Deploy to a remote Docker host via Ansible [cite: 249]

#### Jenkinsfile [cite: 249]

```groovy
pipeline {
    agent any

    environment {
        PROJECT_DIR = "spring-petclinic"
        JAR_PATH = "${PROJECT_DIR}/target/spring-petclinic-3.4.0-SNAPSHOT.jar"
        REMOTE_DEPLOY_DIR = "/root/petclinic-deploy"
        ANSIBLE_DIR = "/root/ansible"
        INVENTORY_FILE = "${ANSIBLE_DIR}/inventory.ini" [cite: 250]
        PLAYBOOK_FILE = "${ANSIBLE_DIR}/deploy-petclinic-with-jar.yml" [cite: 250]
    } [cite: 250]

    stages { [cite: 250]
        stage('Clone Repository') { [cite: 250]
            steps { [cite: 250]
                dir(PROJECT_DIR) { [cite: 250]
                    git branch: 'main', url: '[https://github.com/PushkarrajPujari/spring-petclinic.git](https://github.com/PushkarrajPujari/spring-petclinic.git)' [cite: 250]
                } [cite: 251]
            } [cite: 251]
        } [cite: 251]

        stage('Build JAR with Maven') { [cite: 251]
            steps { [cite: 251]
                dir(PROJECT_DIR) { [cite: 251]
                   sh "mvn clean package -DskipTests" [cite: 251]
                } [cite: 252]
            } [cite: 252]
        } [cite: 252]

        stage('Deploy to Remote via Ansible') { [cite: 252]
            steps { [cite: 252]
                sh "ansible-playbook -i ${INVENTORY_FILE} ${PLAYBOOK_FILE}" [cite: 252]
            } [cite: 252]
        } [cite: 252]
    } [cite: 252]
} [cite: 252]
```
 [cite: 252]

### ✅ Output [cite: 252]

Once deployed, the PetClinic app will be available at the remote host's IP on port `8080`. [cite: 253] Example: http://192.168.0.13:8080 [cite: 254]

### 🧹 Cleanup (Optional) [cite: 254]

To stop and remove the container: [cite: 254]

```sh
docker rm -f petclinic
```
 [cite: 254]

### 📌 Notes [cite: 254]

- Docker comes pre-installed in PWD Alpine nodes. [cite: 255]
- SSH access between nodes is handled using Ansible's configuration to skip host key checks. [cite: 256]
- Jenkins was launched using the WAR file without any additional service manager. [cite: 257]

---
# File: jenkins-notes/lab3/jenkins-trigger.adoc [cite: 257]

## CI/CD: Trigger Jenkins Build on GitHub Merge to Main Branch [cite: 257]
Pushkarraj Pujari <pushkar@example.com> [cite: 257]
:icons: font [cite: 257]
:source-highlighter: coderay [cite: 257]
:toc: left [cite: 257]
:toclevels: 2 [cite: 257]
:sectnums: [cite: 257]

### Objective [cite: 257]

Configure Jenkins to automatically trigger a deployment pipeline whenever a new change is pushed or merged to the `main` branch of a GitHub repository using Webhooks. [cite: 258]

### Environment Setup [cite: 258]

Play with Docker (PWD) environment with Alpine Linux. [cite: 259]

#### Installed Tools [cite: 259]

Required packages were installed via `apk`: [cite: 259]

```sh
#!/bin/sh

echo "Updating package index..."
apk update

echo "Installing required packages: git, OpenJDK 17, Maven, curl, wget, unzip, bash..."
apk add git openjdk17 maven curl wget unzip bash

echo "Downloading latest stable Jenkins WAR file..."
wget [http://mirrors.jenkins.io/war-stable/latest/jenkins.war](http://mirrors.jenkins.io/war-stable/latest/jenkins.war)

echo "Installing font packages required for Jenkins UI..."
apk add --no-cache fontconfig ttf-dejavu

echo "Starting Jenkins in background with logs written to jenkins.log..."
nohup java -jar jenkins.war --httpPort=8080 > jenkins.log 2>&1 &
```
 [cite: 259]

**NOTE:** Docker installation not required as PWD nodes come with Docker pre-installed. [cite: 260]

### Ansible Setup [cite: 260]

Ansible directory structure on Jenkins host: [cite: 260]

```
/root/ansible/
├── deploy-petclinic-with-jar.yml
└── inventory.ini
```
 [cite: 260]

#### inventory.ini [cite: 260]

```ini
[remote]
remote-node ansible_host=192.168.0.13 ansible_user=root ansible_python_interpreter=/usr/bin/python3 ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```
 [cite: 260]

#### deploy-petclinic-with-jar.yml [cite: 260]

```yaml
- name: Deploy PetClinic to remote node
  hosts: remote
  become: true
  tasks:
    - name: Create deployment directory on remote
      file:
        path: /root/petclinic-deploy/target
        state: directory

    - name: Copy built JAR to remote
      copy:
        src: /root/.jenkins/workspace/spring-app-ansible/spring-petclinic/target/spring-petclinic-3.4.0-SNAPSHOT.jar
        dest: /root/petclinic-deploy/target/spring-petclinic-3.4.0-SNAPSHOT.jar

    - name: Copy Dockerfile from project to remote [cite: 261]
      copy: [cite: 261]
        src: /root/.jenkins/workspace/spring-app-ansible/spring-petclinic/Dockerfile [cite: 261]
        dest: /root/petclinic-deploy/Dockerfile [cite: 261]

    - name: Build Docker image on remote [cite: 261]
      shell: | [cite: 261]
        cd /root/petclinic-deploy [cite: 262]
        docker build -t petclinic-app . [cite: 262]
    - name: Run Docker container on remote [cite: 263]
      shell: | [cite: 263]
        docker rm -f petclinic || [cite: 263] true [cite: 264]
        docker run -d -p 8080:8080 --name petclinic petclinic-app [cite: 264]
```
 [cite: 264]

### Jenkins Pipeline [cite: 264]

```groovy
pipeline {
    agent any

    environment {
        PROJECT_DIR = "spring-petclinic"
        JAR_PATH = "${PROJECT_DIR}/target/spring-petclinic-3.4.0-SNAPSHOT.jar"
        REMOTE_DEPLOY_DIR = "/root/petclinic-deploy"
        ANSIBLE_DIR = "/root/ansible"
        INVENTORY_FILE = "${ANSIBLE_DIR}/inventory.ini"
        PLAYBOOK_FILE = "${ANSIBLE_DIR}/deploy-petclinic-with-jar.yml"
    }

    triggers {
        githubPush() [cite: 265]
    } [cite: 265]

    stages { [cite: 265]
        stage('Clone Repository') { [cite: 265]
            steps { [cite: 265]
                dir(PROJECT_DIR) { [cite: 265]
                    git branch: 'main', url: '[https://github.com/PushkarrajPujari/spring-petclinic.git](https://github.com/PushkarrajPujari/spring-petclinic.git)' [cite: 265]
                } [cite: 265]
            } [cite: 266]
        } [cite: 266]

        stage('Build JAR with Maven') { [cite: 266]
            steps { [cite: 266]
                dir(PROJECT_DIR) { [cite: 266]
                   sh "mvn clean package -DskipTests" [cite: 266]
                } [cite: 266]
            } [cite: 267]
        } [cite: 267]

        stage('Deploy to Remote via Ansible') { [cite: 267]
            steps { [cite: 267]
                sh "ansible-playbook -i ${INVENTORY_FILE} ${PLAYBOOK_FILE}" [cite: 267]
            } [cite: 267]
        } [cite: 267]
    } [cite: 267]
} [cite: 267]
```
 [cite: 267]

### GitHub Webhook Setup [cite: 267]

1. Navigate to your GitHub repository → *Settings* → *Webhooks* → *Add webhook* [cite: 268]
2. Set the following values: [cite: 269]
  - *Payload URL*: `http://<jenkins_host>:8080/github-webhook/` [cite: 269]
  - *Content type*: `application/json` [cite: 269]
  - *Events*: Just the push event [cite: 269]
3. Save the webhook [cite: 270]

**NOTE:** If Jenkins is not publicly accessible, you can use tools like `ngrok` to expose it temporarily. [cite: 271]

### Final Result [cite: 271]

Any push or merge to `main` on GitHub will automatically: [cite: 271]

- Trigger Jenkins pipeline [cite: 271]
- Build the project [cite: 271]
- Package it into a JAR [cite: 271]
- Deploy it to a remote Docker container via Ansible [cite: 271]
```
 [cite: 271]
```

### 🎉 Done! [cite: 272]
This setup completes a fully automated CI/CD pipeline triggered by GitHub push events. [cite: 273]

---
# File: jenkins-notes/setup/jenkins-setup-explaination.adoc [cite: 273]

## Setting up Jenkins on Play with Docker (Alpine) [cite: 273]
Author: Your Name [cite: 273]
:icons: font [cite: 273]
:source-highlighter: highlightjs [cite: 273]
:sectnums: [cite: 273]

### Introduction [cite: 273]

This guide provides step-by-step instructions to manually set up Jenkins on _Play with Docker_ using an Alpine Linux environment. [cite: 274] It installs necessary packages, fetches the Jenkins WAR file, resolves font issues, and runs Jenkins in the background with logging. [cite: 274]

### Prerequisites [cite: 275]

- A Play with Docker session: https://labs.play-with-docker.com [cite: 275]
- A running Alpine Linux container [cite: 275]

### Steps [cite: 275]

#### 1. Update Package Index [cite: 275]

Run the following command to update the Alpine package repository: [cite: 275]

```sh
apk update
```
 [cite: 275]

#### 2. Install Required Packages [cite: 275]

Install Java 17, Git, Maven, and other essential tools: [cite: 275]

```sh
apk add git openjdk17 maven curl wget unzip bash
```
 [cite: 275]

#### 3. Download Jenkins WAR File [cite: 275]

Download the latest stable Jenkins WAR file: [cite: 275]

```sh
wget [http://mirrors.jenkins.io/war-stable/latest/jenkins.war](http://mirrors.jenkins.io/war-stable/latest/jenkins.war)
```
 [cite: 275]

#### 4. Fix Missing Fonts Issue [cite: 275]

Jenkins requires some fonts to render the UI properly. [cite: 276] Install them using: [cite: 276]

```sh
apk add --no-cache fontconfig ttf-dejavu
```
 [cite: 276]

#### 5. Start Jenkins in Background with Logging [cite: 276]

Start Jenkins on port 8080 using `nohup` and redirect all logs to `jenkins.log`: [cite: 276]

```sh
nohup java -jar jenkins.war --httpPort=8080 > jenkins.log 2>&1 &
```
 [cite: 276]

You can tail the logs using: [cite: 276]

```sh
tail -f jenkins.log
```
 [cite: 276]

### Access Jenkins [cite: 276]

Once Jenkins is running, access it through your browser using: [cite: 276]

```
http://<your-play-with-docker-instance-ip>:8080
```
 [cite: 276]

Check the terminal logs or the log file for the initial admin password: [cite: 276]

```sh
cat /root/.jenkins/secrets/initialAdminPassword
```
 [cite: 276]

### Conclusion [cite: 276]

You now have Jenkins running in a minimal Alpine-based container using Play with Docker, with logs saved to a file. [cite: 277] You can proceed with installing plugins, setting up jobs, and configuring builds. [cite: 277]

---
# File: jenkins-notes/setup/jenkins-setup.sh [cite: 278]

```sh
#!/bin/sh

echo "Updating package index..."
apk update

echo "Installing required packages: git, OpenJDK 17, Maven, curl, wget, unzip, bash..."
apk add git openjdk17 maven curl wget unzip bash

echo "Downloading latest stable Jenkins WAR file..."
wget [http://mirrors.jenkins.io/war-stable/latest/jenkins.war](http://mirrors.jenkins.io/war-stable/latest/jenkins.war)

echo "Installing font packages required for Jenkins UI..."
apk add --no-cache fontconfig ttf-dejavu

echo "Starting Jenkins in background with logs written to jenkins.log..."
nohup java -jar jenkins.war --httpPort=8080 > jenkins.log 2>&1 &

echo "Jenkins is starting... Tail the log with: tail -f jenkins.log"
```
 [cite: 278]
```
