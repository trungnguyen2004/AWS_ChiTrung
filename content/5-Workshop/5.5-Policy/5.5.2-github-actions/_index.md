---
title: "Configure GitHub Secrets & CI/CD Pipeline"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

In this step, store AWS credentials securely in GitHub Repository Secrets and construct an automated GitHub Actions deployment pipeline.

---

### Step 1: Configure GitHub Repository Secrets

1. Open your source code repository on GitHub at [https://github.com/WhooDuck1810/TrackerMaintenance](https://github.com/WhooDuck1810/TrackerMaintenance).
2. Click **Settings** in the top repository menu tab.
3. In the left sidebar, expand **Secrets and variables** => Click **Actions**.
4. Under **Repository secrets**, click **New repository secret** and add the following 4 secrets:
   - **Name:** `AWS_ACCESS_KEY_ID` | **Value:** Access key for IAM user `tracker-s3-uploader-2`
   - **Name:** `AWS_SECRET_ACCESS_KEY` | **Value:** Secret access key for IAM user `tracker-s3-uploader-2`
   - **Name:** `EC2_HOST` | **Value:** `3.106.194.112` (EC2 Elastic IP)
   - **Name:** `EC2_SSH_KEY` | **Value:** Contents of your private key file (`tracker-key.pem`)

---

### Step 2: Create Workflow Configuration File (`deploy.yml`)

In your repository root, create the directory structure `.github/workflows/` and add a file named `deploy.yml`:

```yaml
name: Deploy to EC2 (Tracker Maintenance)

on:
  push:
    branches:
      - main

jobs:
  build-and-push:
    name: Build & Push Docker Images to ECR
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-southeast-2

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build & Push Backend Docker Image
        run: |
          docker build -t 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com/tracker-be:latest ./tracker_maintenance_service
          docker push 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com/tracker-be:latest

      - name: Build & Push Frontend Docker Image
        run: |
          docker build -t 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com/tracker-fe:latest ./tracker-fe
          docker push 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com/tracker-fe:latest

  deploy:
    name: Deploy Container Stack to EC2 via SSH
    needs: build-and-push
    runs-on: ubuntu-latest
    steps:
      - name: Executing Remote SSH Commands
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ec2-user
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            cd /home/ec2-user
            aws ecr get-login-password --region ap-southeast-2 | docker login --username AWS --password-stdin 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com
            docker compose pull
            docker compose up -d --remove-orphans
```

---

### Step 3: Trigger and Verify Automated Deployment

1. Commit and push your changes to the `main` branch:
   ```bash
   git add .
   git commit -m "Configure CI/CD deployment pipeline"
   git push origin main
   ```
2. Navigate to the **Actions** tab in your GitHub repository.
3. Select the `Deploy to EC2 (Tracker Maintenance)` workflow run.
4. Verify that both `Build & Push` and `Deploy` jobs complete with green checkmarks.

<div style="text-align: center; margin: 20px 0;">

  ![GitHub Actions Runs](github-actions-runs.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.5.2. GitHub Actions automated workflow execution log showing successful Deploy to EC2 runs.</div>
</div>