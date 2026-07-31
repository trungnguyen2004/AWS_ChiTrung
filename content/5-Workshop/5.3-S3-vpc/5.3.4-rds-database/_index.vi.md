---
title: "Triển khai Cơ sở dữ liệu Amazon RDS PostgreSQL"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.3.4. </b> "
---

Trong bước này, bạn sẽ khởi tạo một cơ sở dữ liệu quan hệ PostgreSQL được quản lý tự động trong Private Subnet của `Tracker-VPC`.

---

### Bước 1: Tạo DB Subnet Group

1. Mở **Amazon RDS Console** tại [https://console.aws.amazon.com/rds/](https://console.aws.amazon.com/rds/).
2. Trên menu bên trái, chọn **Subnet groups** => Nhấn **Create DB Subnet Group**.
3. Điền Tên `tracker-rds-subnet-group`, Mô tả `Subnet group cho RDS PostgreSQL`.
4. Chọn VPC `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`.
5. Tại mục **Add subnets**, chọn Vùng `ap-southeast-2a` và chọn `Tracker-VPC-subnet-private1-ap-southeast-2a` (`10.0.128.0/20`).
6. Nhấn **Create**.

---

### Bước 2: Triển khai RDS PostgreSQL Instance (`tracker-maintenance-db`)

1. Trên menu bên trái RDS Console, chọn **Databases** => Nhấn **Create database**.
2. **Phương thức khởi tạo:** Chọn **Standard create**.
3. **Engine options (Loại database):** Chọn **PostgreSQL** (Phiên bản PostgreSQL 15.x).
4. **Templates (Mẫu):** Chọn **Free Tier** (hoặc Dev/Test).
5. **Cấu hình Settings:**
   - **DB instance identifier:** `tracker-maintenance-db`
   - **Master username:** `postgres`
   - **Master password:** Nhập mật khẩu quản trị bảo mật
6. **Cấu hình Instance:**
   - DB instance class: `db.t3.micro` (hoặc `db.t4g.micro`)
7. **Lưu trữ (Storage):**
   - Loại ổ cứng: General Purpose SSD (gp2 / gp3)
   - Dung lượng cấp phát: `20` GiB
8. **Kết nối (Connectivity):**
   - **Virtual Private Cloud (VPC):** `Tracker-VPC-vpc`
   - **DB Subnet group:** `tracker-rds-subnet-group`
   - **Public access (Truy cập công khai):** Chọn **No** (Đảm bảo cách ly riêng tư trong Private Subnet)
   - **VPC security group:** Chọn **Choose existing** => Bỏ chọn `default` => Chọn `ec2-rds-1` (hoặc `tracker-rds-sg`)
9. Nhấn **Create database**.

---

### Bước 3: Kiểm tra Trạng thái Database & Lấy chuỗi Endpoint

1. Chờ vài phút để trạng thái chuyển từ `Creating` sang **`Available`** (Hoạt động).
2. Nhấn vào database `tracker-maintenance-db` để mở trang quản lý tổng quan.
3. Sao chép chuỗi kết nối **Endpoint** (ví dụ `tracker-maintenance-db.cvow26so4q44.ap-southeast-2.rds.amazonaws.com`) và Cổng `5432` để cấu hình kết nối từ phía Backend.

<div style="text-align: center; margin: 20px 0;">

![RDS Database Summary](rds-summary.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.3.5. Tổng quan cơ sở dữ liệu Amazon RDS PostgreSQL trên AWS Console.</div>
</div>
