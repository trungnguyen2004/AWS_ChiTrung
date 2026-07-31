---
title: "Khởi tạo Máy chủ EC2 & Elastic IP"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

Trong bước này, bạn sẽ khởi tạo máy chủ ảo Amazon EC2 (`tracker-maintenance-server`), gán địa chỉ IP tĩnh Elastic IP và cài đặt môi trường thực thi Docker.

---

### Bước 1: Khởi tạo Máy chủ ảo EC2

1. Truy cập **Amazon EC2 Console** => Chọn **Launch instance**.
2. **Tên máy chủ (Name tag):** `tracker-maintenance-server`
3. **Hệ điều hành (AMI):** Chọn **Amazon Linux 2023 AMI**.
4. **Loại máy chủ (Instance type):** Chọn `t3.small` (hoặc `t2.micro` cho Free Tier).
5. **Cặp khóa SSH (Key pair):** Chọn hoặc tạo mới file khóa SSH (`tracker-key.pem`).
6. **Mạng (Network settings):**
   - VPC: `Tracker-VPC-vpc` (`vpc-0a6e694c7f200c12e`)
   - Subnet: `Tracker-VPC-subnet-public1-ap-southeast-2a`
   - Tự động gán IP công khai: Enable
   - Security Group: Chọn `tracker-ec2-sg`

---

### Bước 2: Gắn IAM Role cho Máy chủ EC2

1. Cuộn xuống mục **Advanced details (Chi tiết nâng cao)**.
2. **IAM instance profile:** Chọn `ec2-ecr-role` (hoặc `tracker-ec2-role`).
3. Nhấn **Launch instance**.

---

### Bước 3: Cấp phát & Gán Elastic IP tĩnh

1. Trên menu bên trái EC2 Console, chọn **Elastic IPs** => Nhấn **Allocate Elastic IP address**.
2. Chọn Vùng `ap-southeast-2` => Nhấn **Allocate**.
3. Chọn địa chỉ IP tĩnh vừa tạo (`3.106.194.112`) => Chọn **Actions** => **Associate Elastic IP address**.
4. Chọn Instance: `tracker-maintenance-server` => Nhấn **Associate**.

<div style="text-align: center; margin: 20px 0;">

  ![EC2 Instance Summary](ec2-summary.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.5.1. Chi tiết máy chủ EC2 (tracker-maintenance-server) trạng thái Running kèm Elastic IP trên AWS Console.</div>
</div>

---

### Bước 4: Kết nối SSH & Cài đặt Môi trường Docker

1. Đăng nhập vào máy chủ EC2 qua SSH:
   ```bash
   ssh -i "tracker-key.pem" ec2-user@3.106.194.112
   ```
2. Cập nhật hệ thống và cài đặt Docker:
   ```bash
   sudo yum update -y
   sudo yum install docker -y
   ```
3. Bật dịch vụ Docker và cho phép tự khởi động cùng hệ thống:
   ```bash
   sudo systemctl enable docker
   sudo systemctl start docker
   ```
4. Thêm user `ec2-user` vào nhóm `docker` (giúp chạy lệnh docker không cần `sudo`):
   ```bash
   sudo usermod -aG docker ec2-user
   ```
5. Cài đặt plugin Docker Compose:
   ```bash
   sudo mkdir -p /usr/libexec/docker/cli-plugins
   sudo curl -SL https://github.com/docker/compose/releases/download/v2.24.0/docker-compose-linux-x86_64 -o /usr/libexec/docker/cli-plugins/docker-compose
   sudo chmod +x /usr/libexec/docker/cli-plugins/docker-compose
   ```
6. Kiểm tra phiên bản cài đặt thành công:
   ```bash
   docker --version
   docker compose version
   ```