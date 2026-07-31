---
title: "Configure Security Groups for EC2 & RDS"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

In this step, configure two virtual firewall security groups to enforce isolation between the public web instance and the private database.

---

### Step 1: Create EC2 Web Security Group (`tracker-ec2-sg` / `launch-wizard-2`)

1. Open the **Amazon EC2 Console** => In the left navigation pane under **Network & Security**, click **Security Groups**.
2. Click **Create security group**.
3. Configure basic details:
   - **Security group name:** `tracker-ec2-sg` (or `launch-wizard-2`)
   - **Description:** Security group for EC2 web server and Docker application containers
   - **VPC:** Select `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`
4. Under **Inbound rules**, click **Add rule** to add the following 5 rules:
   - **Rule 1 (SSH):** Type: `SSH` | Port: `22` | Source: `0.0.0.0/0` (Remote management)
   - **Rule 2 (HTTP):** Type: `HTTP` | Port: `80` | Source: `0.0.0.0/0` (Web traffic)
   - **Rule 3 (HTTPS):** Type: `HTTPS` | Port: `443` | Source: `0.0.0.0/0` (SSL Web traffic)
   - **Rule 4 (Frontend Container):** Type: `Custom TCP` | Port: `3000` | Source: `0.0.0.0/0`
   - **Rule 5 (Backend Container API):** Type: `Custom TCP` | Port: `8081` | Source: `0.0.0.0/0`
5. Click **Create security group**.

---

### Step 2: Create RDS Database Security Group (`ec2-rds-1`)

1. Click **Create security group** again.
2. Configure details:
   - **Security group name:** `ec2-rds-1` (or `tracker-rds-sg`)
   - **Description:** Security group isolating RDS PostgreSQL database
   - **VPC:** Select `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`
3. Under **Inbound rules**, click **Add rule**:
   - **Type:** `PostgreSQL`
   - **Port range:** `5432`
   - **Source:** Custom => Select Security Group ID of EC2 (`sg-030d01ed40d06b8f2 / tracker-ec2-sg`)
4. Click **Create security group**.

<div style="text-align: center; margin: 20px 0;">

![Security Groups Rules](security-groups-rules.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.3.4. Inbound and Outbound Security Group rules for EC2 server and RDS database connection.</div>
</div>
