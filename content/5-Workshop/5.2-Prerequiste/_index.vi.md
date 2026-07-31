---
title: "Chuẩn bị"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

Trước khi bắt đầu triển khai dự án **Tracker Maintenance System** trên AWS, cần chuẩn bị môi trường máy tính cá nhân và các quyền truy cập IAM an toàn.

---

### Bước 5.2.1: Chuẩn bị Môi trường Máy cá nhân (Local Workstation)

1. Cài đặt **JDK 21 (Amazon Corretto hoặc Temurin)** và **Node.js v20 LTS** trên máy cá nhân.
2. Cài đặt **Docker Desktop & Git** để đóng gói và kiểm thử container cục bộ.
3. Chuẩn bị file `.env` cá nhân và đảm bảo các thông tin nhạy cảm (`AWS_ACCESS_KEY_ID`, `JWT_SECRET`, mật khẩu database) đã được khai báo trong `.gitignore`.

---

### Bước 5.2.2: Khởi tạo IAM User duy nhất (`tracker-s3-uploader-2`) cho S3 & ECR

Để cấp quyền cho Backend Spring Boot tải ảnh nghiệm thu lên S3 và cho phép GitHub Actions đẩy Docker Image lên Amazon ECR, khởi tạo 01 IAM User duy nhất (`tracker-s3-uploader-2`):

1. Truy cập **AWS IAM Console** => **Users** => Chọn **Create user**.
2. Nhập tên user: `tracker-s3-uploader-2`.
3. Tại phần **Permissions options**, chọn **Attach policies directly**.
4. Gắn 02 chính sách quyền chuẩn của AWS:
   - `AmazonEC2ContainerRegistryFullAccess` (Quyền quản lý và push Docker image ECR)
   - `AmazonS3FullAccess` (Quyền upload và truy xuất hình ảnh S3)
5. Xác nhận tạo user, sau đó vào mục **Security credentials** => **Create access key** => Chọn **Command Line Interface (CLI)**.
6. Lưu lại cặp khóa **Access Key ID** và **Secret Access Key** để nạp vào `.env` backend và GitHub Secrets.

<div style="text-align: center; margin: 20px 0;">

![Khởi tạo IAM User](iam-user-setup.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.2.1. Chi tiết IAM User (tracker-s3-uploader-2) với 2 chính sách AmazonEC2ContainerRegistryFullAccess và AmazonS3FullAccess đính kèm.</div>
</div>

---

### Bước 5.2.3: Khởi tạo IAM Role cho Máy chủ EC2

Cấp quyền cho máy chủ EC2 kéo Docker Image từ ECR và đẩy log về CloudWatch mà không cần lưu khóa truy cập cố định trên máy chủ:

1. Truy cập **IAM Console** => **Roles** => Chọn **Create role**.
2. Chọn Trusted entity type: **AWS Service**, trường hợp sử dụng: **EC2**.
3. Gắn 02 chính sách quyền chuẩn của AWS:
   - `AmazonEC2ContainerRegistryReadOnly`
   - `CloudWatchAgentServerPolicy`
4. Đặt tên Role: `tracker-ec2-role` và nhấn **Create role**.

---

### Bước 5.2.4: Tại sao cần dùng IAM User (`tracker-s3-uploader-2`) thay vì Root Account hoặc `admin1`?

- **Nguyên tắc Quyền Tối thiểu (Principle of Least Privilege):** Chỉ cấp đúng quyền S3/ECR cho ứng dụng qua `tracker-s3-uploader-2` thay vì dùng tài khoản quản trị toàn quyền (`admin1` / Root).
- **Cách ly Rủi ro Bảo mật:** Nếu Access Key bị lộ, rủi ro chỉ giới hạn trong phạm vi S3/ECR mà không làm mất kiểm soát toàn bộ tài khoản AWS.
- **Khả năng Truy vết (Auditability):** Mọi hành động gọi API S3/ECR đều được AWS CloudTrail ghi lại rõ danh tính `tracker-s3-uploader-2`.
