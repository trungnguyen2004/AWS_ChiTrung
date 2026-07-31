---
title: "Hướng dẫn Dọn dẹp Tài nguyên (Stop vs Terminate)"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.6.3. </b> "
---

Thực hiện theo các bước chi tiết dưới đây để tạm dừng hoặc hủy bỏ hoàn toàn các tài nguyên AWS nhằm tránh phát sinh chi phí ngoài ý muốn.

---

### Bước 1: Tạm dừng Tài nguyên (Stop Instance)

Nếu bạn có kế hoạch tiếp tục làm bài lab sau này:

1. **Tạm dừng Máy chủ EC2:**
   - Mở **EC2 Console** => **Instances** => Chọn `tracker-maintenance-server`.
   - Nhấn **Instance state** => Chọn **Stop instance**.
   - *Tác động chi phí:* Phí máy chủ ($0/giờ), chỉ tính phí lưu trữ ổ cứng EBS rất nhỏ (~$0.08/GB/tháng).
2. **Tạm dừng Cơ sở dữ liệu RDS:**
   - Mở **RDS Console** => **Databases** => Chọn `tracker-maintenance-db`.
   - Nhấn **Actions** => Chọn **Stop temporarily**.
   - *Tác động chi phí:* Cho phép dừng tối đa 7 ngày, chỉ tính phí lưu trữ ổ cứng.

---

### Bước 2: Dọn dẹp Lưu trữ ẢNH & Container Images

1. **Xóa dữ liệu trên S3 Bucket:**
   - Mở **S3 Console** => Chọn `tracker-maintenance-images-123` => Nhấn **Empty** => Nhập `permanently delete`.
2. **Xóa Docker Image trên ECR:**
   - Mở **ECR Console** => Chọn `tracker-be` và `tracker-fe` => Chọn các tag image => Nhấn **Delete**.

---

### Bước 3: Hủy bỏ Hoàn toàn Tài nguyên (Kết thúc Thực tập)

Nếu muốn xóa sạch toàn bộ môi trường Cloud:

1. **Xóa vĩnh viễn EC2:** EC2 Console => Chọn `tracker-maintenance-server` => Instance state => **Terminate instance**.
2. **Xóa vĩnh viễn RDS:** RDS Console => Chọn `tracker-maintenance-db` => Actions => **Delete** (Bỏ tích tạo bản snapshot cuối).
3. **Xóa S3 Bucket:** S3 Console => Chọn `tracker-maintenance-images-123` => Nhấn **Delete**.
4. **Xóa ECR Repositories:** ECR Console => Chọn các repository => Nhấn **Delete**.
5. **Giải phóng Elastic IP:** EC2 Console => Elastic IPs => Chọn `3.106.194.112` => Actions => **Release Elastic IP address**.
6. **Xóa Hosted Zone & Chứng chỉ ACM:** Xóa Route 53 hosted zone và chứng chỉ ACM.