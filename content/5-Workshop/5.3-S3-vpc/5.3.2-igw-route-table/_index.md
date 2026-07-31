---
title: "Configure Internet Gateway & Route Tables"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

In this step, connect your public subnet to the internet by creating and attaching an Internet Gateway (`Tracker-VPC-igw`) and configuring route tables.

---

### Step 1: Create Internet Gateway (`Tracker-VPC-igw`)

1. In the **VPC Console** left navigation pane, click **Internet gateways**.
2. Click **Create internet gateway**.
3. In the **Name tag** field, enter `Tracker-VPC-igw`.
4. Click **Create internet gateway**.

---

### Step 2: Attach Internet Gateway to Custom VPC

1. Select the created Internet Gateway (`igw-0f51a5a491c36f5ae / Tracker-VPC-igw`).
2. Click **Actions** in the top right corner => Choose **Attach to VPC**.
3. Under **Available VPCs**, select `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`.
4. Click **Attach internet gateway**.
5. Verify that the **State** updates to **Attached** (green checkmark).

<div style="text-align: center; margin: 20px 0;">

![Internet Gateway Setup](igw-setup.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.3.3. Internet Gateway (Tracker-VPC-igw) details showing Attached state to Tracker-VPC-vpc.</div>
</div>

---

### Step 3: Configure Public Route Table (`Tracker-VPC-rtb-public`)

1. In the VPC Console left navigation pane, click **Route tables**.
2. Select the public route table associated with `Tracker-VPC-vpc` (Name: `Tracker-VPC-rtb-public`).
3. Click the **Routes** tab => Click **Edit routes**.
4. Click **Add route** and enter:
   - **Destination:** `0.0.0.0/0` (All outbound internet traffic)
   - **Target:** Select **Internet Gateway** => Choose `igw-0f51a5a491c36f5ae / Tracker-VPC-igw`
5. Click **Save changes**.

---

### Step 4: Associate Public Subnet to Route Table

1. Click the **Subnet associations** tab on `Tracker-VPC-rtb-public`.
2. Click **Edit subnet associations**.
3. Select `Tracker-VPC-subnet-public1-ap-southeast-2a` => Click **Save associations**.
