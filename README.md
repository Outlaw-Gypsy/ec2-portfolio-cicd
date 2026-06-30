# EC2 CI/CD Portfolio Deployment with GitHub Actions

## 📌 Project Overview

This project demonstrates a complete CI/CD pipeline that automates the deployment of a static portfolio website to an AWS EC2 Ubuntu server using GitHub Actions, Git, SSH, and Nginx.

Any push to the `main` branch automatically triggers deployment to the EC2 instance, ensuring the live website is always up to date.

---

## 🧠 CI/CD Architecture

Developer (Mac) → GitHub Repository → GitHub Actions → EC2 Instance → Nginx Web Server → Browser

---

## 🛠️ Technologies Used

- macOS (local development environment)
- Git & GitHub (version control)
- AWS EC2 (Ubuntu Server)
- Nginx (web server)
- GitHub Actions (CI/CD automation)
- SSH (secure remote access)

---

## 🧱 STEP 1 — Local Project Setup (Mac)

### 1.1 Create project directory

```bash
mkdir ec2-portfolio-cicd
cd ec2-portfolio-cicd
```

### 1.2 Create portfolio website files

index.html
projects.html
contact.html
html/
  ├── index.html
  ├── projects.html
  └── contact.html

### 1.3 Initialize Git repository
```bash
git init
```

### 1.4 Stage and commit files
```bash
git add .
git commit -m "Initial portfolio website"
```

### 1.5 Create GitHub repository
```text
Repository Name: ec2-portfolio-cicd
```

### 1.6 Connect local repository to GitHub
```bash
git remote add origin https://github.com/<username>/ec2-portfolio-cicd.git
git branch -M main
git push -u origin main
```

## ☁️ STEP 2 — AWS EC2 Setup

### 2.1 Launch EC2 instance

- Ubuntu Server
- Public IP enabled
- Security Group:
	- SSH (22)
	- HTTP (80)

### 2.2 Networking fix (Troubleshooting)

Issue: SSH timeout

Fix: Add route to Internet Gateway

0.0.0.0/0 → Internet Gateway

### 2.3 SSH into EC2
```bash
ssh -i CICD_key.pem ubuntu@<EC2_PUBLIC_IP>
```

### 2.4 Install dependencies
```bash
sudo apt update
sudo apt install nginx git -y
```

### 2.5 Verify Nginx
```text
http://<EC2_PUBLIC_IP>
```

---

## 📂 STEP 3 — Application Deployment (Manual Setup)

### 3.1 Create project directory on EC2
```bash
mkdir -p ~/portfolio
cd ~/portfolio
```

### 3.2 Clone GitHub repository
```bash
git clone https://github.com/<username>/ec2-portfolio-cicd.git
cd ec2-portfolio-cicd
```

### 3.3 Copy website files to Nginx root
```bash
sudo cp -r html/* /var/www/html/
```

## ⚠️ TROUBLESHOOTING — Deployment Issue

Issue: Default Nginx page still showing

Cause: Incorrect copy path (nested folder structure)

Fix:

```bash
sudo cp -r html/* /var/www/html/
```

---

## ⚙️ STEP 4 — GitHub Actions CI/CD Pipeline

### 4.1 Create workflow directory
```bash
mkdir -p .github/workflows
```

### 4.2 Create workflow file
```bash
.github/workflows/deploy.yml
```

### 4.3 CI/CD pipeline configuration

```yaml

name: Deploy to EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Deploy to EC2 via SSH
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            cd ~/portfolio/ec2-portfolio-cicd || git clone https://github.com/<username>/ec2-portfolio-cicd.git ~/portfolio/ec2-portfolio-cicd
            cd ~/portfolio/ec2-portfolio-cicd
            git pull origin main
            sudo cp -r html/* /var/www/html/
            sudo systemctl restart nginx
```

---

## 🔐 STEP 5 — GitHub Secrets Configuration

Go to your repository on GitHub, then got to the setting, and then go to Secrets and Variables and then click on Actions. You should be able to create these three repository secrets.

EC2_HOST       = <EC2_PUBLIC_IP>
EC2_USERNAME   = ubuntu
EC2_SSH_KEY    = <private_key_content>

---

## 🚀 STEP 6 — CI/CD Testing

### 6.1 Make a change

```text
<h1>CI/CD Pipeline Working 🚀</h1>
```

### 6.2 Commit and push changes

```bash
git add .
git commit -m "Test CI/CD pipeline"
git push
```

### 6.3 Pipeline execution

- GitHub Actions triggered
- SSH connection to EC2 established
- Latest code pulled
- Website deployed

### 6.4 Verification

```text
http://<EC2_PUBLIC_IP>
```

## 🧠 STEP 8 — Key Learnings
- EC2 networking and Internet Gateway configuration
- Nginx static file serving
- Git-based deployment workflow
- SSH-based remote automation
- CI/CD pipeline automation using GitHub Actions

---

## ⚠️ TROUBLESHOOTING SUMMARY

### SSH timeout issue

Cause: Missing route to Internet Gateway

Fix: Added 0.0.0.0/0 → Internet Gateway

### Nginx default page issue

Cause: Incorrect file copy path

Fix: Used html/* instead of *

### Git push workflow error

Cause: PAT missing workflow scope

Fix: Regenerated token with correct permissions. I added permission for workflow in my newly created token to push the project folder to GitHub.

## 🎯 CONCLUSION

This project implements a fully automated CI/CD pipeline that deploys a static website to AWS EC2 using GitHub Actions, demonstrating real-world DevOps deployment practices.





































