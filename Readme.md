
# DevOps Practical Questions & Commands

---

## 1. Basic Git Commands

```bash
# Initialize a Git Repository
$ git init

# Clone a Remote Repository
$ git clone <repository_url>

# Add files to staging
$ git add <filename>  # or use . to add all files

# Commit changes
$ git commit -m "Your commit message"

# Push changes to GitHub
$ git push origin <branch_name>

# Pull changes from Remote
$ git pull origin <branch_name>
```

---

## 2. Initialize and Push to a Local Git Repository

```bash
$ mkdir my_project && cd my_project
$ git init
$ touch README.md
$ git add README.md
$ git commit -m "Initial commit"
$ git remote add origin <repository_url>
$ git push -u origin master
```

---

## 3. Clone a Remote Repository and Make Changes

```bash
$ git clone <repository_url>
$ cd <repo_folder>
$ echo "New change" >> change.txt
$ git add change.txt
$ git commit -m "Added change.txt"
$ git push origin main
```

---

## 4. Pull Changes from Remote Repository

```bash
$ git pull origin main
```

---

## 5. CI/CD with Jenkins

```text
- Install Jenkins and start the service.
- Create a new freestyle project.
- Connect to GitHub via source code management.
- Add build step: Execute shell script (e.g., build and deploy commands).
- Add post-build action: Archive artifacts or send notifications.
```

---

## 6. Basic Docker Commands

```bash
# Docker version
$ docker --version

# List running containers
$ docker ps

# List all containers
$ docker ps -a

# Start a container
$ docker start <container_id>

# Stop a container
$ docker stop <container_id>

# Restart a container
$ docker restart <container_id>

# Remove a stopped container
$ docker rm <container_id>

# View logs
$ docker logs <container_id>

# List docker images
$ docker images

# Pull image
$ docker pull ubuntu

# Build docker image
$ docker build -t my_image .

# Remove an image
$ docker rmi <image_id>

# Remove unused images
$ docker image prune -a
```

---

## 7. Git & GitHub Project Workflow

```bash
# Initialize Repository
$ git init

# Create Branch and Merge
$ git checkout -b new-feature
# make changes
$ git commit -am "Added new feature"
$ git checkout main
$ git merge new-feature

# Push to GitHub
$ git push origin main

# Fork/Edit/PR: Use GitHub interface to fork -> edit -> create PR
```

---

## 8. Web App Deployment via Jenkins

```text
- Create a freestyle Jenkins project
- Connect GitHub repo in Source Code Management
- Build Step: Shell Script
  $ npm install
  $ npm run build
  $ serve -s build (for local HTTP server)
```

---

## 9. GitLab Web IDE Tasks

```text
- Create New Project
- Use Web IDE to create/edit files
- Create Branches
- Make Changes and Commit
- Create Merge Request
- Setup CI/CD pipeline via `.gitlab-ci.yml`
```

---

## 10. Linux Ops via Docker Ubuntu

```bash
$ docker pull ubuntu
$ docker run -it ubuntu /bin/bash
# Inside container:
$ mkdir docker_test
$ cd docker_test
$ echo "Docker Testing" > info.txt
$ exit

# Back to host:
$ docker ps -a
```

---

## 11. GitLab Merge Request Workflow

```bash
# Fork and Clone
$ git clone <forked_repo>

# New Branch and Changes
$ git checkout -b new-feature
$ echo "update" >> file.txt
$ git add file.txt
$ git commit -m "Update file.txt"
$ git push origin new-feature

# On GitLab:
- Create Merge Request
- Reviewer reviews and merges it
- Resolve conflicts manually if required
```

---

## 12. Basic Ansible Commands

```bash
# Ping all hosts
$ ansible all -m ping

# List hosts
$ ansible all --list-hosts

# Run ad-hoc command
$ ansible all -m shell -a "uptime"
```

---

## 13. Ansible Playbook Example

```yaml
# playbook.yml
---
- hosts: all
  become: yes
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
```

Run the playbook:
```bash
$ ansible-playbook -i inventory.txt playbook.yml
```

---

## ?? Note:
Ensure Docker, Git, Jenkins, Ansible are installed and properly configured before running these commands.
