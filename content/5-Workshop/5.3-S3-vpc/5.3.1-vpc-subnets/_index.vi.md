---
title: "Khởi tạo Custom VPC & Phân chia Subnet (Public/Private)"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

Trong bước này, bạn sẽ khởi tạo một mạng riêng ảo cách ly (`Tracker-VPC-vpc`) và phân chia dải mạng Public Subnet (cho máy chủ web EC2) và Private Subnet (cho cơ sở dữ liệu RDS).

---

### Bước 1: Khởi tạo Custom VPC (`Tracker-VPC-vpc`)

1. Mở **Amazon VPC Console** tại địa chỉ [https://console.aws.amazon.com/vpc/](https://console.aws.amazon.com/vpc/).
2. Kiểm tra góc trên bên phải đảm bảo Vùng AWS đang chọn là **ap-southeast-2 (Sydney)**.
3. Tại trang VPC Dashboard, nhấn nút **Create VPC**.
4. Tại mục **Resources to create**, tích chọn **VPC only**.
5. Điền các thông số cấu hình:
   - **Name tag:** `Tracker-VPC-vpc`
   - **IPv4 CIDR block:** Nhập thủ công `10.0.0.0/16`
   - **IPv6 CIDR block:** No IPv6 CIDR block
   - **Tenancy:** Default
6. Nhấn **Create VPC**.
7. Chọn VPC vừa tạo (`vpc-0a6e694c7f200c12e`) => Nhấn **Actions** => **Edit VPC settings** => Tích chọn **Enable DNS hostnames** và **Enable DNS resolution** => Nhấn **Save**.

---

### Bước 2: Khởi tạo Public Subnet (`Tracker-VPC-subnet-public1-ap-southeast-2a`)

1. Trên thanh danh mục bên trái của VPC Console, chọn **Subnets**.
2. Nhấn nút **Create subnet**.
3. Tại mục **VPC ID**, chọn `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`.
4. Nhập các thông số cho Subnet 1:
   - **Subnet name:** `Tracker-VPC-subnet-public1-ap-southeast-2a`
   - **Availability Zone:** `ap-southeast-2a`
   - **IPv4 VPC CIDR block:** `10.0.0.0/16`
   - **IPv4 subnet CIDR block:** `10.0.0.0/20`
5. Nhấn **Create subnet**.
6. Chọn Subnet công khai vừa tạo => Chọn **Actions** => **Edit subnet settings** => Tích chọn **Enable auto-assign public IPv4 address** (Tự động gán IP công khai) => Nhấn **Save**.

---

### Bước 3: Khởi tạo Private Subnet (`Tracker-VPC-subnet-private1-ap-southeast-2a`)

1. Tiếp tục nhấn nút **Create subnet**.
2. Tại mục **VPC ID**, chọn `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`.
3. Nhập các thông số cho Subnet 2:
   - **Subnet name:** `Tracker-VPC-subnet-private1-ap-southeast-2a`
   - **Availability Zone:** `ap-southeast-2a`
   - **IPv4 VPC CIDR block:** `10.0.0.0/16`
   - **IPv4 subnet CIDR block:** `10.0.128.0/20`
4. Nhấn **Create subnet**. (Giữ nguyên không bật tự động gán IP công khai để đảm bảo tính riêng tư hoàn toàn cho RDS).

---

### Bước 4: Kiểm tra Sơ đồ Tài nguyên VPC Resource Map

1. Chọn `Tracker-VPC-vpc` trong danh sách VPCs.
2. Cuộn xuống tab **Resource map** để xem sơ đồ trực quan thể hiện mối liên kết giữa VPC, các Subnet và Route Tables.

<div style="text-align: center; margin: 20px 0;">

![Sơ đồ tài nguyên VPC](images/vpc-resource-map.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.3.1. Sơ đồ tài nguyên (Resource Map) của Tracker-VPC-vpc hiển thị liên kết giữa VPC, Subnet, Bảng tuyến đường và Internet Gateway.</div>
</div>

<div style="text-align: center; margin: 20px 0;">

![Danh sách Subnet](images/subnets-list.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.3.2. Danh sách Subnet công khai và riêng tư được khởi tạo trong Tracker-VPC-vpc.</div>

</div>
