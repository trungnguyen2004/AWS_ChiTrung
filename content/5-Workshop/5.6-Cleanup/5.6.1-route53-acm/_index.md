---
title: "Configure Route 53 DNS & ACM SSL Certificate"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

In this step, map your custom domain (purchased externally) to AWS Route 53 and register an SSL/TLS Certificate in AWS Certificate Manager (ACM).

---

### Step 1: External Domain & Route 53 Hosted Zone Setup

1. The custom domain `trackermaint.dpdns.org` was acquired from an external domain registrar.
2. Open the **Amazon Route 53 Console** at [https://console.aws.amazon.com/route53/](https://console.aws.amazon.com/route53/).
3. In the left navigation pane, click **Hosted zones** => Click **Create hosted zone**.
4. Configure domain settings:
   - **Domain name:** `trackermaint.dpdns.org`
   - **Type:** Public hosted zone
5. Click **Create hosted zone**.
6. Copy the 4 assigned AWS Name Servers (`ns-581.awsdns-08.net`, etc.) and set them as custom DNS Name Servers at your domain registrar.

---

### Step 2: Create A Record (DNS Routing to Server)

1. Inside your `trackermaint.dpdns.org` hosted zone, click **Create record**.
2. Configure record parameters:
   - **Record name:** `trackermaint.dpdns.org` (or leave empty for root domain)
   - **Record type:** `A - Routes traffic to an IPv4 address and some AWS resources`
   - **Value:** `3.106.194.112` (EC2 Elastic IP address or Load Balancer dualstack endpoint)
   - **TTL:** 300 seconds
3. Click **Create records**.

<div style="text-align: center; margin: 20px 0;">

  ![Route 53 Records](route53-records.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.6.1. Route 53 Hosted Zone records for trackermaint.dpdns.org showing A Record, NS, SOA, and CNAME validation.</div>
</div>

---

### Step 3: Request ACM SSL Certificate (`Issued`)

1. Open the **AWS Certificate Manager (ACM) Console** at [https://console.aws.amazon.com/acm/](https://console.aws.amazon.com/acm/).
2. In the top-right corner, ensure Region is set to `us-east-1` (N. Virginia) for CloudFront compatibility or `ap-southeast-2` (Sydney).
3. Click **Request certificate** => Select **Request a public certificate** => Click **Next**.
4. Enter Fully qualified domain name: `trackermaint.dpdns.org`.
5. Select **DNS validation - recommended** => Click **Request**.
6. Open the requested certificate details page => Click **Create records in Route 53** to automatically insert the CNAME domain validation record into Route 53.
7. Wait 1-2 minutes for validation. Verify that the **Status** updates to **Issued** (green checkmark).

<div style="text-align: center; margin: 20px 0;">

  ![ACM Certificate Status](acm-certificate.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.6.2. AWS Certificate Manager (ACM) dashboard displaying Issued status for trackermaint.dpdns.org.</div>
</div>