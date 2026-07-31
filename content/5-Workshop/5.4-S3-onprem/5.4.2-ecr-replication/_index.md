---
title: "Configure Amazon ECR & Cross-Region Replication"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

In this step, create private Docker repositories on Amazon ECR for backend (`tracker-be`) and frontend (`tracker-fe`) images, and configure automatic Cross-Region Replication to secondary regions (Singapore & Ohio).

---

### Step 1: Create Backend Private Repository (`tracker-be`)

1. Open the **Amazon ECR Console** at [https://console.aws.amazon.com/ecr/](https://console.aws.amazon.com/ecr/).
2. In the left navigation pane under **Private registry**, click **Repositories**.
3. Click **Create repository**.
4. Configure settings:
   - **Visibility settings:** Select **Private**
   - **Repository name:** `tracker-be`
   - **Tag immutability:** Mutable
   - **Encryption configuration:** AES-256 (Default)
5. Click **Create repository**.

---

### Step 2: Create Frontend Private Repository (`tracker-fe`)

1. Click **Create repository** again.
2. Set **Visibility settings** to **Private**.
3. Set **Repository name** to `tracker-fe`.
4. Click **Create repository**.

> [!NOTE]
> ℹ️ **Important Note regarding Repository List:** In the screenshot below, the repository `aws/tracker_maintenance_app` was an initial experimental testing repository. Please focus exclusively on the two production repositories: **`tracker-be`** and **`tracker-fe`**.

<div style="text-align: center; margin: 20px 0;">

  ![ECR Repositories](ecr-repositories.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.4.2. Amazon ECR Private Repositories list displaying tracker-be and tracker-fe container images.</div>
</div>

---

### Step 3: Configure Cross-Region Registry Replication Rules

To enable multi-region readiness (automatically replicating pushed Sydney images to Singapore and Ohio):

1. In the ECR Console left navigation pane under **Private registry**, click **Registry settings**.
2. Click **Replication configuration** => Click **Edit**.
3. Click **Add rule**.
4. Set Destination regions: Select **ap-southeast-1 (Singapore)** and **us-east-2 (Ohio)**.
5. Set Repository filter: Select **All repositories** (or prefix `tracker-`).
6. Click **Save rule**.

---

### Step 4: Obtain ECR Push Commands

To view CLI login and push instructions for any repository:
1. Select `tracker-be` in the repositories list.
2. Click **View push commands** in the top-right corner.
3. Note the login authentication command:
   ```bash
   aws ecr get-login-password --region ap-southeast-2 | docker login --username AWS --password-stdin 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com
   ```