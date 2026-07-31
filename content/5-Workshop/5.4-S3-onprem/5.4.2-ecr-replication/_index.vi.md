---
title: "Cấu hình Amazon ECR & Cross-Region Replication (Singapore & Ohio)"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

Trong bước này, bạn sẽ tạo các kho lưu trữ Docker Registry riêng tư trên Amazon ECR cho image backend (`tracker-be`) và frontend (`tracker-fe`), đồng thời cấu hình tính năng sao chép tự động đa vùng Cross-Region Replication sang các vùng phụ (Singapore & Ohio).

---

### Bước 1: Tạo Backend Private Repository (`tracker-be`)

1. Mở **Amazon ECR Console** tại [https://console.aws.amazon.com/ecr/](https://console.aws.amazon.com/ecr/).
2. Trên menu bên trái dưới mục **Private registry**, chọn **Repositories**.
3. Nhấn nút **Create repository**.
4. Điền các thông số:
   - **Visibility settings:** Chọn **Private**
   - **Repository name:** `tracker-be`
   - **Tag immutability:** Mutable
   - **Encryption configuration:** AES-256 (Mặc định)
5. Nhấn **Create repository**.

---

### Bước 2: Tạo Frontend Private Repository (`tracker-fe`)

1. Tiếp tục nhấn **Create repository**.
2. Chọn **Visibility settings** là **Private**.
3. Nhập **Repository name** là `tracker-fe`.
4. Nhấn **Create repository**.

> [!NOTE]
> ℹ️ **Lưu ý quan trọng về danh sách Repository:** Trong hình ảnh bên dưới, repository `aws/tracker_maintenance_app` là kho lưu trữ thử nghiệm ban đầu. Bạn vui lòng tập trung vào 02 repository chính thức phục vụ triển khai hệ thống là: **`tracker-be`** và **`tracker-fe`**.

<div style="text-align: center; margin: 20px 0;">

  ![ECR Repositories](ecr-repositories.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.4.2. Danh sách Amazon ECR Private Repositories bao gồm tracker-be và tracker-fe.</div>
</div>

---

### Bước 3: Cấu hình Quy tắc Sao chép đa vùng Cross-Region Replication

Để sẵn sàng cho kiến trúc đa vùng (tự động sao chép các Docker Image push về Sydney sang Singapore và Ohio):

1. Trên menu bên trái ECR Console dưới mục **Private registry**, chọn **Registry settings**.
2. Chọn **Replication configuration** => Nhấn **Edit**.
3. Nhấn **Add rule**.
4. Chọn Vùng đích (Destination regions): Chọn **ap-southeast-1 (Singapore)** và **us-east-2 (Ohio)**.
5. Bộ lọc Repository: Chọn **All repositories** (hoặc tiền tố `tracker-`).
6. Nhấn **Save rule**.

---

### Bước 4: Xem Lệnh Đăng nhập & Push Image ECR

Để xem hướng dẫn lệnh CLI đăng nhập và push image cho từng repository:
1. Chọn `tracker-be` trong danh sách repository.
2. Nhấn nút **View push commands** ở góc trên bên phải.
3. Ghi lại lệnh xác thực đăng nhập Docker:
   ```bash
   aws ecr get-login-password --region ap-southeast-2 | docker login --username AWS --password-stdin 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com
   ```