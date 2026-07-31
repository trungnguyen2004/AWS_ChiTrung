---
title: "Create Custom VPC & Subnets"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

In this step, provision an isolated virtual private network (`Tracker-VPC-vpc`) and allocate public and private subnets across availability zones.

---

### Step 1: Create Custom VPC (`Tracker-VPC-vpc`)

1. Open the **Amazon VPC Console** at [https://console.aws.amazon.com/vpc/](https://console.aws.amazon.com/vpc/).
2. In the top-right corner, verify that your AWS Region is set to **ap-southeast-2 (Sydney)**.
3. In the VPC Dashboard, click **Create VPC**.
4. Under **Resources to create**, select **VPC only**.
5. Configure the following parameters:
   - **Name tag:** `Tracker-VPC-vpc`
   - **IPv4 CIDR block:** Manual input `10.0.0.0/16`
   - **IPv6 CIDR block:** No IPv6 CIDR block
   - **Tenancy:** Default
6. Click **Create VPC**.
7. Select the created VPC (`vpc-0a6e694c7f200c12e`) => Click **Actions** => **Edit VPC settings** => Check **Enable DNS hostnames** and **Enable DNS resolution** => Click **Save**.

---

### Step 2: Create Public Subnet (`tracker-public-subnet-1`)

1. In the left navigation pane of the VPC Console, click **Subnets**.
2. Click **Create subnet**.
3. Under **VPC ID**, select `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`.
4. Configure Subnet 1 parameters:
   - **Subnet name:** `Tracker-VPC-subnet-public1-ap-southeast-2a`
   - **Availability Zone:** `ap-southeast-2a`
   - **IPv4 VPC CIDR block:** `10.0.0.0/16`
   - **IPv4 subnet CIDR block:** `10.0.0.0/20`
5. Click **Create subnet**.
6. Select the newly created public subnet => Click **Actions** => **Edit subnet settings** => Check **Enable auto-assign public IPv4 address** => Click **Save**.

---

### Step 3: Create Private Subnet (`tracker-private-subnet-1`)

1. Click **Create subnet** again.
2. Under **VPC ID**, select `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`.
3. Configure Subnet 2 parameters:
   - **Subnet name:** `Tracker-VPC-subnet-private1-ap-southeast-2a`
   - **Availability Zone:** `ap-southeast-2a`
   - **IPv4 VPC CIDR block:** `10.0.0.0/16`
   - **IPv4 subnet CIDR block:** `10.0.128.0/20`
4. Click **Create subnet**. (Leave auto-assign public IP unchecked to keep it strictly private for RDS).

---

### Step 4: Verify Infrastructure in VPC Resource Map

1. Select `Tracker-VPC-vpc` in the VPCs list.
2. Scroll down to the **Resource map** tab to visually verify the relationship between your VPC, Subnets, and Route Tables.

<div style="text-align: center; margin: 20px 0;">

![Tracker VPC Resource Map](images/vpc-resource-map.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.3.1. AWS VPC Resource Map showing Tracker-VPC-vpc, Subnets, Route Tables, and Internet Gateway connection.</div>
</div>

<div style="text-align: center; margin: 20px 0;">

![Tracker Subnets List](images/subnets-list.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.3.2. Subnet allocation list showing public and private subnets inside Tracker-VPC-vpc.</div>
</div>
