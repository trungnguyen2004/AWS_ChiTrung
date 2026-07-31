---
title: "Provision Amazon RDS PostgreSQL Database"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.3.4. </b> "
---

In this step, deploy a managed PostgreSQL relational database instance inside the Private Subnet of `Tracker-VPC`.

---

### Step 1: Create DB Subnet Group

1. Open the **Amazon RDS Console** at [https://console.aws.amazon.com/rds/](https://console.aws.amazon.com/rds/).
2. In the left navigation pane, click **Subnet groups** => Click **Create DB Subnet Group**.
3. Set Name to `tracker-rds-subnet-group`, Description `Subnet group for RDS PostgreSQL`.
4. Select VPC `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`.
5. Under **Add subnets**, select Availability Zone `ap-southeast-2a` and choose `Tracker-VPC-subnet-private1-ap-southeast-2a` (`10.0.128.0/20`).
6. Click **Create**.

---

### Step 2: Provision RDS PostgreSQL Instance (`tracker-maintenance-db`)

1. In the RDS Console left navigation pane, click **Databases** => Click **Create database**.
2. **Database creation method:** Select **Standard create**.
3. **Engine options:** Select **PostgreSQL** (Version: PostgreSQL 15.x).
4. **Templates:** Select **Free Tier** (or Dev/Test).
5. **Settings:**
   - **DB instance identifier:** `tracker-maintenance-db`
   - **Master username:** `postgres`
   - **Master password:** Set a strong password (e.g., `YOUR_SECURE_DB_PASSWORD`)
6. **Instance configuration:**
   - DB instance class: `db.t3.micro` (or `db.t4g.micro`)
7. **Storage:**
   - Storage type: General Purpose SSD (gp2 / gp3)
   - Allocated storage: `20` GiB
8. **Connectivity:**
   - **Virtual Private Cloud (VPC):** `Tracker-VPC-vpc`
   - **DB Subnet group:** `tracker-rds-subnet-group`
   - **Public access:** Select **No** (Enforces private subnet isolation)
   - **VPC security group:** Select **Choose existing** => Remove `default` => Select `ec2-rds-1` (or `tracker-rds-sg`)
9. Click **Create database**.

---

### Step 3: Verify Database Instance Status & Endpoint

1. Wait a few minutes for the status to change from `Creating` to **`Available`**.
2. Click `tracker-maintenance-db` to open its summary dashboard.
3. Copy the **Endpoint** connection string (e.g. `tracker-maintenance-db.cvow26so4q44.ap-southeast-2.rds.amazonaws.com`) and Port `5432` for application configuration.

<div style="text-align: center; margin: 20px 0;">

![RDS Database Summary](rds-summary.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.3.5. Amazon RDS PostgreSQL instance summary showing Available status and private connectivity settings.</div>
</div>
