---
title: "Provision EC2 Instance & Elastic IP"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

In this step, launch the Amazon EC2 virtual server (`tracker-maintenance-server`), associate a static Elastic IP address, and install Docker runtimes.

---

### Step 1: Launch EC2 Virtual Server

1. Open the **Amazon EC2 Console** => Click **Launch instance**.
2. **Name tag:** `tracker-maintenance-server`
3. **Application and OS Image (AMI):** Select **Amazon Linux 2023 AMI**.
4. **Instance type:** Select `t3.small` (or `t2.micro` for Free Tier).
5. **Key pair:** Select or create an SSH key pair (`tracker-key.pem`).
6. **Network settings:**
   - VPC: `Tracker-VPC-vpc` (`vpc-0a6e694c7f200c12e`)
   - Subnet: `Tracker-VPC-subnet-public1-ap-southeast-2a`
   - Auto-assign Public IP: Enable
   - Security Group: Select `tracker-ec2-sg`

---

### Step 2: Associate IAM Role to EC2 Server

1. Scroll down to **Advanced details**.
2. **IAM instance profile:** Select `ec2-ecr-role` (or `tracker-ec2-role`).
3. Click **Launch instance**.

---

### Step 3: Allocate & Associate Elastic IP

1. In the EC2 Console left navigation pane, choose **Elastic IPs** => Click **Allocate Elastic IP address**.
2. Choose Region `ap-southeast-2` => Click **Allocate**.
3. Select the allocated Elastic IP (`3.106.194.112`) => Click **Actions** => **Associate Elastic IP address**.
4. Choose Instance: `tracker-maintenance-server` => Click **Associate**.

<div style="text-align: center; margin: 20px 0;">

  ![EC2 Instance Summary](ec2-summary.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.5.1. EC2 Instance summary for tracker-maintenance-server showing Running state, Elastic IP, and IAM role.</div>
</div>

---

### Step 4: SSH Connection & Docker Installation

1. Connect to the EC2 server using SSH:
   ```bash
   ssh -i "tracker-key.pem" ec2-user@3.106.194.112
   ```
2. Update system packages and install Docker:
   ```bash
   sudo yum update -y
   sudo yum install docker -y
   ```
3. Start the Docker daemon service and enable it on system boot:
   ```bash
   sudo systemctl enable docker
   sudo systemctl start docker
   ```
4. Add the `ec2-user` to the `docker` user group (enabling non-root docker execution):
   ```bash
   sudo usermod -aG docker ec2-user
   ```
5. Install Docker Compose plugin:
   ```bash
   sudo mkdir -p /usr/libexec/docker/cli-plugins
   sudo curl -SL https://github.com/docker/compose/releases/download/v2.24.0/docker-compose-linux-x86_64 -o /usr/libexec/docker/cli-plugins/docker-compose
   sudo chmod +x /usr/libexec/docker/cli-plugins/docker-compose
   ```
6. Verify Docker installation:
   ```bash
   docker --version
   docker compose version
   ```