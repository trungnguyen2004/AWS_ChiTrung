---
title: "Cấu hình Tên miền Route 53 & Chứng chỉ ACM SSL"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

Trong bước này, bạn sẽ kết nối tên miền mua từ nhà cung cấp bên ngoài (`trackermaint.dpdns.org`) vào AWS Route 53 và đăng ký cấp chứng chỉ bảo mật SSL/TLS miễn phí qua AWS Certificate Manager (ACM).

---

### Bước 1: Khởi tạo Route 53 Hosted Zone cho Tên miền Mua ngoài

1. Tên miền cá nhân `trackermaint.dpdns.org` được đăng ký từ nhà cung cấp tên miền ngoài.
2. Mở **Amazon Route 53 Console** tại [https://console.aws.amazon.com/route53/](https://console.aws.amazon.com/route53/).
3. Trên menu bên trái, chọn **Hosted zones** => Nhấn **Create hosted zone**.
4. Cấu hình thông tin:
   - **Domain name:** `trackermaint.dpdns.org`
   - **Type:** Public hosted zone
5. Nhấn **Create hosted zone**.
6. Sao chép 4 địa chỉ máy chủ DNS AWS Name Servers (`ns-581.awsdns-08.net`,...) và cấu hình chuyển tiếp DNS tại trang quản lý tên miền ngoài.

---

### Bước 2: Tạo Bản ghi A Record (Định tuyến về Máy chủ)

1. Trong trang quản lý Hosted Zone `trackermaint.dpdns.org`, nhấn **Create record**.
2. Điền thông số bản ghi:
   - **Record name:** `trackermaint.dpdns.org`
   - **Record type:** `A - Routes traffic to an IPv4 address and some AWS resources`
   - **Value:** `3.106.194.112` (Địa chỉ Elastic IP của EC2 hoặc Load Balancer dualstack endpoint)
   - **TTL:** 300 seconds
3. Nhấn **Create records**.

<div style="text-align: center; margin: 20px 0;">

  ![Route 53 Records](route53-records.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.6.1. Danh sách bản ghi Route 53 Hosted Zone cho tên miền trackermaint.dpdns.org bao gồm A Record, NS, SOA và CNAME xác minh.</div>
</div>

---

### Bước 3: Đăng ký Chứng chỉ SSL trên AWS Certificate Manager (ACM)

1. Mở **AWS Certificate Manager (ACM) Console** tại [https://console.aws.amazon.com/acm/](https://console.aws.amazon.com/acm/).
2. Đảm bảo chọn Vùng `us-east-1` (cho CloudFront) hoặc `ap-southeast-2` (Sydney).
3. Nhấn **Request certificate** => Chọn **Request a public certificate** => Nhấn **Next**.
4. Nhập Tên miền đầy đủ: `trackermaint.dpdns.org`.
5. Chọn **DNS validation - recommended** => Nhấn **Request**.
6. Mở chi tiết chứng chỉ vừa tạo => Nhấn **Create records in Route 53** để tự động chèn bản ghi CNAME xác minh vào Route 53.
7. Chờ 1-2 phút. Xác nhận trạng thái **Status** đổi sang **Issued** (biểu tượng tích xanh).

<div style="text-align: center; margin: 20px 0;">

  ![ACM Certificate Status](acm-certificate.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.6.2. Trang quản lý chứng chỉ ACM hiển thị trạng thái Issued cho tên miền trackermaint.dpdns.org.</div>
</div>