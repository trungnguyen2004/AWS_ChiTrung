---
title: "Cấu hình Tường lửa ảo (Security Groups) cho EC2 & RDS"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

Trong bước này, bạn sẽ cấu hình 02 tường lửa ảo (Security Groups) để cách ly an toàn giữa ứng dụng Web công khai và cơ sở dữ liệu riêng tư.

---

### Bước 1: Tạo EC2 Security Group (`tracker-ec2-sg` / `launch-wizard-2`)

1. Mở **Amazon EC2 Console** => Trên menu bên trái dưới mục **Network & Security**, chọn **Security Groups**.
2. Nhấn nút **Create security group**.
3. Điền các thông số cơ bản:
   - **Security group name:** `tracker-ec2-sg` (hoặc `launch-wizard-2`)
   - **Description:** Tường lửa cho EC2 web server và Docker containers
   - **VPC:** Chọn `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`
4. Tại mục **Inbound rules**, nhấn **Add rule** để thêm 5 quy tắc cổng mở:
   - **Rule 1 (SSH):** Type: `SSH` | Port: `22` | Source: `0.0.0.0/0` (Quản trị từ xa)
   - **Rule 2 (HTTP):** Type: `HTTP` | Port: `80` | Source: `0.0.0.0/0` (Lưu lượng Web)
   - **Rule 3 (HTTPS):** Type: `HTTPS` | Port: `443` | Source: `0.0.0.0/0` (Lưu lượng Web SSL)
   - **Rule 4 (Frontend Container):** Type: `Custom TCP` | Port: `3000` | Source: `0.0.0.0/0`
   - **Rule 5 (Backend Container API):** Type: `Custom TCP` | Port: `8081` | Source: `0.0.0.0/0`
5. Nhấn **Create security group**.

---

### Bước 2: Tạo RDS Database Security Group (`ec2-rds-1`)

1. Tiếp tục nhấn **Create security group**.
2. Điền thông số:
   - **Security group name:** `ec2-rds-1` (hoặc `tracker-rds-sg`)
   - **Description:** Tường lửa bảo vệ cơ sở dữ liệu RDS PostgreSQL
   - **VPC:** Chọn `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`
3. Tại mục **Inbound rules**, nhấn **Add rule**:
   - **Type:** `PostgreSQL`
   - **Port range:** `5432`
   - **Source:** Custom => Chọn ID Security Group của EC2 (`sg-030d01ed40d06b8f2 / tracker-ec2-sg`)
4. Nhấn **Create security group**.

<div style="text-align: center; margin: 20px 0;">

![Security Groups Rules](security-groups-rules.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.3.4. Chi tiết Inbound và Outbound Rules của Security Group trên AWS Console.</div>
</div>
