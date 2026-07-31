---
title: "Configure Amazon S3 Bucket for Media Storage"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

In this step, create an Amazon S3 Bucket to store uploaded equipment inspection photos and maintenance evidence offloaded from EC2.

---

### Step 1: Create Amazon S3 Bucket (`tracker-maintenance-images-123`)

1. Open the **Amazon S3 Console** at [https://console.aws.amazon.com/s3/](https://console.aws.amazon.com/s3/).
2. Click **Create bucket**.
3. Configure general configuration:
   - **Bucket name:** `tracker-maintenance-images-123` (Globally unique bucket name)
   - **AWS Region:** `ap-southeast-2` (Sydney)
4. **Object Ownership:** Select **ACLs disabled (recommended)**.
5. **Block Public Access settings for this bucket:**
   - Uncheck **Block _all_ public access** (Disable).
   - Check the acknowledgement box: _"I acknowledge that the current settings might result in this bucket and the objects within it becoming public."_
6. Click **Create bucket**.

---

### Step 2: Configure Public Read Bucket Policy

1. In the S3 Console, click `tracker-maintenance-images-123` => Click the **Permissions** tab.
2. Scroll down to **Bucket policy** => Click **Edit**.
3. Paste the following JSON policy to grant public read access (`s3:GetObject`) for all uploaded images:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::tracker-maintenance-images-123/*"
    }
  ]
}
```

4. Click **Save changes**.

<div style="text-align: center; margin: 20px 0;">

![S3 Bucket Policy](s3-policy.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.4.1. Amazon S3 Bucket Permissions showing Block Public Access settings and PublicReadGetObject policy.</div>
</div>

---

### Step 3: Configure Cross-Origin Resource Sharing (CORS)

1. On the **Permissions** tab, scroll down to **Cross-origin resource sharing (CORS)** => Click **Edit**.
2. Paste the following CORS JSON configuration to allow `GET`, `PUT`, and `POST` requests from the web frontend:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
    "AllowedOrigins": [
      "https://trackermaint.dpdns.org",
      "http://localhost:3000"
    ],
    "ExposeHeaders": ["ETag"]
  }
]
```

3. Click **Save changes**.
