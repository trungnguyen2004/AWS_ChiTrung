---
title: "Cấu hình Internet Gateway & Bảng tuyến đường (Route Tables)"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

Trong bước này, bạn sẽ mở kết nối Internet cho dải Public Subnet bằng cách tạo Cổng Internet (Internet Gateway `Tracker-VPC-igw`), Attach vào VPC và cấu hình Bảng tuyến đường Route Table.

---

### Bước 1: Khởi tạo Internet Gateway (`Tracker-VPC-igw`)

1. Trên menu bên trái VPC Console, chọn **Internet gateways**.
2. Nhấn nút **Create internet gateway**.
3. Tại ô **Name tag**, nhập: `Tracker-VPC-igw`.
4. Nhấn **Create internet gateway**.

---

### Bước 2: Kết nối Internet Gateway vào Custom VPC

1. Chọn Internet Gateway vừa tạo (`igw-0f51a5a491c36f5ae / Tracker-VPC-igw`).
2. Nhấn nút **Actions** ở góc trên bên phải => Chọn **Attach to VPC**.
3. Tại mục **Available VPCs**, chọn `vpc-0a6e694c7f200c12e | Tracker-VPC-vpc`.
4. Nhấn **Attach internet gateway**.
5. Xác nhận trạng thái **State** đổi sang **Attached** (có biểu tượng tích xanh).

<div style="text-align: center; margin: 20px 0;">

![Cấu hình Internet Gateway](igw-setup.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.3.3. Chi tiết Internet Gateway (Tracker-VPC-igw) trạng thái Attached thành công vào Tracker-VPC-vpc.</div>
</div>

---

### Bước 3: Cấu hình Bảng tuyến đường công khai (`Tracker-VPC-rtb-public`)

1. Trên menu bên trái VPC Console, chọn **Route tables**.
2. Chọn Bảng tuyến đường công khai của `Tracker-VPC-vpc` (Tên: `Tracker-VPC-rtb-public`).
3. Chọn tab **Routes** => Nhấn nút **Edit routes**.
4. Nhấn **Add route** và nhập thông số:
   - **Destination:** `0.0.0.0/0` (Toàn bộ lưu lượng đi ra ngoài Internet)
   - **Target:** Chọn **Internet Gateway** => Chọn `igw-0f51a5a491c36f5ae / Tracker-VPC-igw`
5. Nhấn **Save changes**.

---

### Bước 4: Gán Public Subnet vào Bảng tuyến đường

1. Chọn tab **Subnet associations** trên Bảng tuyến đường `Tracker-VPC-rtb-public`.
2. Nhấn **Edit subnet associations**.
3. Tích chọn `Tracker-VPC-subnet-public1-ap-southeast-2a` => Nhấn **Save associations**.
