---
title: "Resource Clean Up Guide (Stop vs Terminate)"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.6.3. </b> "
---

Follow these step-by-step instructions to temporarily pause or permanently decommission AWS resources to prevent unexpected billing.

---

### Step 1: Temporary Resource Pause (Stop Instance)

If you plan to resume the workshop later, stop your compute and database instances:

1. **Pause EC2 Instance:**
   - Open **EC2 Console** => **Instances** => Select `tracker-maintenance-server`.
   - Click **Instance state** => Click **Stop instance**.
   - *Cost impact:* Compute ($0/hour), only minimal EBS storage costs (~$0.08/GB/month).
2. **Pause RDS Database:**
   - Open **RDS Console** => **Databases** => Select `tracker-maintenance-db`.
   - Click **Actions** => Click **Stop temporarily**.
   - *Cost impact:* RDS can remain stopped for up to 7 days. You pay only for allocated storage.

---

### Step 2: Delete Temporary Storage & Container Images

1. **Empty S3 Bucket:**
   - Open **S3 Console** => Select `tracker-maintenance-images-123` => Click **Empty** => Type `permanently delete`.
2. **Delete ECR Images:**
   - Open **ECR Console** => Select `tracker-be` and `tracker-fe` => Select all image tags => Click **Delete**.

---

### Step 3: Permanent Decommissioning (End of Project)

If permanently deleting the entire lab environment:

1. **Terminate EC2 Server:** EC2 Console => Select `tracker-maintenance-server` => Instance state => **Terminate instance**.
2. **Delete RDS Database:** RDS Console => Select `tracker-maintenance-db` => Actions => **Delete** (Uncheck final snapshot).
3. **Delete S3 Bucket:** S3 Console => Select `tracker-maintenance-images-123` => Click **Delete**.
4. **Delete ECR Repositories:** ECR Console => Select repositories => Click **Delete**.
5. **Release Elastic IP:** EC2 Console => Elastic IPs => Select `3.106.194.112` => Actions => **Release Elastic IP address**.
6. **Delete Hosted Zone & ACM Cert:** Delete Route 53 hosted zone and ACM certificate.