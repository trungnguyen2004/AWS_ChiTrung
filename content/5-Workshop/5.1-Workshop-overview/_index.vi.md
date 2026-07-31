---
title: "Giới thiệu"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

<div style="text-align: center; margin: 20px 0;">

![TrackerMaintenance Architecture](AWS_Architecture_new.png)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 1. Kiến trúc tổng thể hạ tầng mạng và tích hợp các dịch vụ AWS cho dự án Tracker Maintenance.</div>
</div>

<br>

## 1. Tổng quan dự án

**Hệ thống Quản lý và Bảo trì Thiết bị (Tracker Maintenance System)** là một ứng dụng Web full-stack cloud-native được thiết kế để quản lý, theo dõi và bảo trì toàn bộ vòng đời của các thiết bị công nghiệp. Hệ thống cho phép các tổ chức số hóa hoàn toàn quy trình bảo trì — từ việc báo cáo sự cố thiết bị, phân công kỹ thuật viên đến việc lập lịch bảo trì định kỳ, tải lên hình ảnh nghiệm thu và xuất báo cáo — tất cả trên một nền tảng Web tập trung phân quyền theo vai trò (Role-Based Access Control).

Toàn bộ dự án được phát triển và triển khai 100% trên nền tảng **Amazon Web Services (AWS)** trong khuôn khổ chương trình thực tập theo **Đề tài 3: Xây dựng và phát triển ứng dụng Web trên nền tảng Cloud**.

## 2. Đặt vấn đề & Vấn đề nghiệp vụ

Trong môi trường quản lý nhà xưởng và công nghiệp truyền thống, việc bảo trì thiết bị thường được quản lý thủ công qua sổ sách, file Excel hoặc các phần mềm On-Premises rời rạc. Phương pháp này bộc lộ nhiều hạn chế nghiêm trọng:

- **Thiếu khả năng theo dõi thời gian thực (Real-time visibility):** Quản lý không thể biết chính xác thiết bị nào đang hỏng, kỹ thuật viên nào đang xử lý, hoặc tiến độ sửa chữa ra sao nếu không gọi điện hoặc kiểm tra trực tiếp.
- **Không có kênh báo sự cố tập trung:** Nhân viên thông thường (Reporter) không có kênh chính thức để gửi yêu cầu sửa chữa, thường phải gửi qua tin nhắn cá nhân gây thất lạc thông tin.
- **Thiếu bằng chứng nghiệm thu:** Không có nhật ký lưu trữ hình ảnh hoặc mốc thời gian hoàn thành, dẫn đến khó kiểm tra chất lượng sửa chữa hoặc truy xuất lịch sử sự cố.
- **Lập lịch bảo trì thủ công:** Việc phân công bảo trì định kỳ dễ bị bỏ sót hoặc trùng lặp công việc.
- **Rủi ro bảo mật:** Các hệ thống đăng nhập đơn giản dễ bị tấn công dò mật khẩu (brute-force), đe dọa an toàn dữ liệu vận hành.

**Tracker Maintenance System** giải quyết triệt để các vấn đề trên bằng cách cung cấp giải pháp Web trên Cloud tích hợp thông báo thời gian thực, quét mã QR quản lý tài sản, lưu trữ ảnh S3 và cơ chế chống tấn công brute-force tự động.

## 3. Mục tiêu bài lab

Bài lab hướng dẫn các mục tiêu kỹ thuật cốt lõi sau:

1. **Phát triển ứng dụng Web Full-Stack:** Xây dựng ứng dụng Web hoàn chỉnh với Backend Spring Boot 3.5 (Java 21) REST API và Frontend ReactJS (React Router v7, TypeScript, TailwindCSS).
2. **Triển khai hạ tầng Cloud trên AWS:** Triển khai toàn bộ ứng dụng lên các dịch vụ AWS bao gồm EC2, RDS PostgreSQL, S3, ECR, CloudWatch, IAM và CloudFront.
3. **Đóng gói Container với Docker:** Đóng gói cả hai dịch vụ Frontend và Backend thành các Docker Container sử dụng Multi-stage build và điều phối bằng Docker Compose trên máy chủ EC2.
4. **Bảo mật xác thực — Chống tấn công Brute-Force:** Triển khai hệ thống giới hạn tần suất truy cập (rate-limiting) và khóa tài khoản tạm thời lưu trên bộ nhớ RAM để bảo vệ endpoint đăng nhập khỏi các cuộc tấn công tự động.
5. **Tự động hóa CI/CD Pipeline:** Thiết lập luồng GitHub Actions tự động build, push Docker image lên Amazon ECR và deploy lên máy chủ EC2 mỗi khi push code lên nhánh `main`.
6. **Thông báo thời gian thực (Real-Time Notifications):** Triển khai WebSocket (SockJS + STOMP) để đẩy thông báo ngay lập tức đến người dùng khi có ticket mới, cập nhật trạng thái hoặc phân công công việc.
7. **Quản lý tài sản qua mã QR (QR Code Asset Tracking):** Tự động tạo mã QR cho từng thiết bị, cho phép kỹ thuật viên quét mã QR bằng điện thoại để xem ngay thông tin chi tiết và lịch sử bảo trì.
8. **Lưu trữ đa phương tiện trên Cloud (Amazon S3):** Lưu trữ toàn bộ ảnh thiết bị và ảnh nghiệm thu trên Amazon S3, giảm tải hoàn toàn lưu trữ cho máy chủ EC2.
9. **Quản lý log tập trung với CloudWatch:** Đẩy toàn bộ log container ứng dụng (backend và frontend) về AWS CloudWatch Log Groups để theo dõi và tìm kiếm thời gian thực.
10. **Sao chép ECR đa vùng (Cross-Region Replication):** Cấu hình quy tắc nhân bản registry ECR để hình ảnh Docker push về vùng chính Sydney tự động đồng bộ sang các vùng phụ (Singapore, Ohio), sẵn sàng cho việc mở rộng đa vùng trong tương lai.

## 4. Sơ đồ kiến trúc hệ thống

Hệ thống tuân theo kiến trúc **Cloud-native đa tầng (Multi-tier)** triển khai tại vùng AWS `ap-southeast-2 (Sydney)`:

```
Người dùng (Trình duyệt)
    │
    ▼
Internet
    │
    ▼
Amazon CloudFront (CDN & Edge / SSL Termination)
    │
    ▼
Internet Gateway (VPC Entry Point)
    │
    ▼
┌─────────────────── VPC (ap-southeast-2) ──────────────────┐
│  ┌────── Public Subnet ──────┐  ┌──── Private Subnet ────┐ │
│  │                           │  │                        │ │
│  │  EC2 Instance             │  │  Amazon RDS            │ │
│  │  ├─ Docker: tracker-fe    │◄─┤  PostgreSQL            │ │
│  │  │  (React Router/Node.js)│  │  (db.t4g.micro)        │ │
│  │  └─ Docker: tracker-be    │  │                        │ │
│  │     (Spring Boot API)     │  └────────────────────────┘ │
│  └───────────┬───────────────┘                             │
│              │ Tải ảnh                Log                   │
│              ▼                         ▼                   │
│  ┌── Amazon S3 ──┐       ┌── CloudWatch Logs ──┐          │
│  │  Images Bucket │       │  /tracker-maint/be  │          │
│  │  (Public Read) │       │  /tracker-maint/fe  │          │
│  └───────────────┘       └─────────────────────┘          │
│                                                             │
│  ┌── IAM ────────────────────────────────────────────┐    │
│  │  EC2 Role, GitHub Actions User, S3 Access Keys    │    │
│  └───────────────────────────────────────────────────┘    │
└────────────────────────────────────────────────────────────┘

Amazon ECR (ap-southeast-2)
  ├─ tracker-be:latest
  └─ tracker-fe:latest
       │ Sao chép đa vùng (Cross-Region Replication)
       ├──► ECR ap-southeast-1 (Singapore)
       └──► ECR us-east-2 (Ohio)

Quy trình CI/CD GitHub Actions
  push main ──► Build Docker ──► Push ECR Sydney ──► SSH Deploy EC2
```

## 5. Tóm tắt Công nghệ sử dụng

| Tầng               | Công nghệ                | Phiên bản  | Mục đích                                   |
| ------------------ | ------------------------ | ---------- | ------------------------------------------ |
| Frontend Framework | React Router             | v7 (SSR)   | Ứng dụng Single Page rendering phía server |
| Frontend Language  | TypeScript               | 5.x        | Ngôn ngữ Frontend an toàn kiểu dữ liệu     |
| Frontend Styling   | TailwindCSS              | v3         | CSS responsive dạng utility-first          |
| Backend Framework  | Spring Boot              | 3.5        | Server REST API                            |
| Backend Language   | Java                     | 21 (LTS)   | Xử lý logic nghiệp vụ Backend              |
| Backend Security   | Spring Security + JWT    | 6.x        | Xác thực & phân quyền người dùng           |
| Database           | PostgreSQL               | 15         | Cơ sở dữ liệu quan hệ                      |
| Container Runtime  | Docker + Docker Compose  | 24.x       | Đóng gói ứng dụng                          |
| Container Registry | Amazon ECR               | -          | Quản lý Docker image riêng tư              |
| Compute            | Amazon EC2               | t2.micro   | Máy chủ ảo Cloud                           |
| Database Service   | Amazon RDS               | PostgreSQL | Cơ sở dữ liệu Cloud quản lý tự động        |
| Object Storage     | Amazon S3                | -          | Lưu trữ hình ảnh và tập tin đa phương tiện |
| CDN                | Amazon CloudFront        | -          | Mạng phân phối nội dung toàn cầu & SSL     |
| Logging            | Amazon CloudWatch        | -          | Quản lý và giám sát log tập trung          |
| Identity           | AWS IAM                  | -          | Quản lý quyền và truy cập                  |
| DNS                | Route 53 + dpdns.org     | -          | Quản lý tên miền hệ thống                  |
| CI/CD              | GitHub Actions           | -          | Tự động hóa build và deploy                |
| Real-time          | WebSocket (SockJS/STOMP) | -          | Hệ thống đẩy thông báo thời gian thực      |
| Code Repository    | GitHub                   | -          | Quản lý mã nguồn dự án                     |
