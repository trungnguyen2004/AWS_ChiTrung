---
title: "Cấu hình GitHub Secrets & Luồng Tự động hóa CI/CD"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

Trong bước này, bạn sẽ lưu trữ các thông tin xác thực an toàn trong GitHub Repository Secrets và tự động hóa luồng triển khai lên máy chủ AWS EC2.

---

### Bước 1: Cấu hình GitHub Repository Secrets

1. Mở mã nguồn dự án trên GitHub tại [https://github.com/WhooDuck1810/TrackerMaintenance](https://github.com/WhooDuck1810/TrackerMaintenance).
2. Nhấn vào tab **Settings** ở thanh menu trên cùng.
3. Trên danh mục bên trái, mở rộng mục **Secrets and variables** => Chọn **Actions**.
4. Tại mục **Repository secrets**, nhấn **New repository secret** để thêm 4 thông số bí mật:
   - **Name:** `AWS_ACCESS_KEY_ID` | **Value:** Access key của IAM user `tracker-s3-uploader-2`
   - **Name:** `AWS_SECRET_ACCESS_KEY` | **Value:** Secret access key của IAM user `tracker-s3-uploader-2`
   - **Name:** `EC2_HOST` | **Value:** `3.106.194.112` (Elastic IP của EC2)
   - **Name:** `EC2_SSH_KEY` | **Value:** Nội dung file khóa SSH bí mật (`tracker-key.pem`)

---

### Bước 2: Tạo File Cấu hình Workflow (`deploy.yml`)

Tại thư mục gốc của mã nguồn, tạo cấu trúc thư mục `.github/workflows/` và tạo file tên `deploy.yml`:

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

### Bước 3: Kích hoạt & Kiểm tra Nhật ký Triển khai

1. Push mã nguồn lên nhánh `main`:
   ```bash
   git add .
   git commit -m "Configure CI/CD deployment pipeline"
   git push origin main
   ```
2. Mở tab **Actions** trên GitHub Repository.
3. Chọn luồng chạy `Deploy to EC2 (Tracker Maintenance)`.
4. Xác nhận cả 2 công việc `Build & Push` và `Deploy` đều hoàn thành với biểu tượng tích xanh.

<div style="text-align: center; margin: 20px 0;">

  ![Lịch sử chạy GitHub Actions](github-actions-runs.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.5.2. Nhật ký chạy thành công các luồng tự động hóa Deploy to EC2 trên GitHub Actions.</div>
</div>