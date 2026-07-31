---
title: "Cấu hình Amazon S3 Bucket lưu trữ ảnh nghiệm thu"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

Trong bước này, bạn sẽ tạo Amazon S3 Bucket để lưu trữ ảnh thiết bị và bằng chứng bảo trì tải lên từ phía ứng dụng Web.

---

### Bước 1: Khởi tạo Amazon S3 Bucket (`tracker-maintenance-images-123`)

1. Mở **Amazon S3 Console** tại [https://console.aws.amazon.com/s3/](https://console.aws.amazon.com/s3/).
2. Nhấn nút **Create bucket**.
3. Điền các thông tin cấu hình chung:
   - **Bucket name:** `tracker-maintenance-images-123` (Tên bucket duy nhất trên toàn cầu)
   - **AWS Region:** `ap-southeast-2` (Sydney)
4. **Object Ownership:** Chọn **ACLs disabled (recommended)**.
5. **Block Public Access settings for this bucket (Quyền truy cập công khai):**
   - Bỏ tích **Block *all* public access** (Tắt chặn công khai).
   - Tích chọn ô xác nhận: *"I acknowledge that the current settings might result in this bucket and the objects within it becoming public."*
6. Nhấn **Create bucket**.

---

### Bước 2: Cấu hình Chính sách Đọc công khai Bucket Policy

1. Trong S3 Console, chọn bucket `tracker-maintenance-images-123` => Chọn tab **Permissions**.
2. Cuộn xuống mục **Bucket policy** => Nhấn **Edit**.
3. Dán đoạn mã JSONBucket Policy cho phép đọc công khai (`s3:GetObject`) các tập tin ảnh:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::tracker-maintenance-images-123/*"
    }
  ]
}
```
4. Nhấn **Save changes**.

<div style="text-align: center; margin: 20px 0;">

  ![S3 Bucket Policy](s3-policy.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.4.1. Cấu hình quyền S3 Bucket bao gồm Block Public Access Off và chính sách đọc công khai Bucket Policy.</div>
</div>

---

### Bước 3: Cấu hình Quy tắc Chia sẻ Tài nguyên CORS

1. Trên tab **Permissions**, cuộn xuống mục **Cross-origin resource sharing (CORS)** => Nhấn **Edit**.
2. Dán đoạn cấu hình CORS JSON cho phép các yêu cầu `GET`, `PUT`, `POST` từ trình duyệt ứng dụng Web:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
    "AllowedOrigins": ["https://trackermaint.dpdns.org", "http://localhost:3000"],
    "ExposeHeaders": ["ETag"]
  }
]
```
3. Nhấn **Save changes**.