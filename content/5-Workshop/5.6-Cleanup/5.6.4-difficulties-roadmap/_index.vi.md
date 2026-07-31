---
title: "Khó khăn gặp phải & Hướng phát triển tương lai"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.6.4. </b> "
---

Trong phần cuối này, bạn sẽ tổng kết các thách thức kỹ thuật gặp phải trong quá trình triển khai hệ thống lên Cloud, nguyên nhân gốc rễ, giải pháp đã áp dụng và định hướng nâng cấp kiến trúc trong tương lai.

---

### 1. Khó khăn Kỹ thuật & Giải pháp

#### Khó khăn 1.1: Lỗi đường dẫn Windows vs. Linux khi Build React Router
- **Nguyên nhân gốc rễ:** Chạy `npm run build` trên hệ điều hành Windows sinh ra file bundle chứa dấu gạch ngược đường dẫn kiểu Windows (`build\client`). Khi đóng gói vào Linux Docker container trên EC2, thư viện `@react-router/serve` không thể tìm thấy module, gây lỗi HTTP 404 cho tất cả file JS/CSS.
- **Giải pháp áp dụng:** Chuyển toàn bộ luồng build Docker Image sang môi trường Linux Runner của GitHub Actions (`ubuntu-latest`). Việc build trên Linux đảm bảo ký tự phân cách luôn là dấu gạch chéo `/` chuẩn. Nguyên tắc: *Không bao giờ build Docker image của Frontend trực tiếp trên Windows*.

#### Khó khăn 1.2: Giới hạn RAM trên EC2 Free Tier & Lỗi Tràn bộ nhớ (OOM)
- **Nguyên nhân gốc rễ:** Chạy đồng thời cả Java 21 Spring Boot JVM và Node.js server trên instance `t2.micro` (1 GB RAM) làm kích hoạt cơ chế Linux OOM Killer, tự động tắt tiến trình backend.
- **Giải pháp áp dụng:** Cấu hình giới hạn bộ nhớ heap cho JVM trong `docker-compose.yml`:
  ```yaml
  JAVA_TOOL_OPTIONS: "-Xms256m -Xmx512m"
  ```
  Giới hạn bộ nhớ heap tối đa ở mức 512 MB giúp hệ thống luôn còn đủ RAM trống cho Node.js và các tiến trình hệ điều hành.

#### Khó khăn 1.3: Yêu cầu Xác minh Tài khoản khi khởi tạo CloudFront
- **Nguyên nhân gốc rễ:** Khởi tạo CloudFront Distribution bị tạm khóa do yêu cầu xác minh tài khoản từ AWS (*"Your account must be verified before you can add new CloudFront resources"*).
- **Giải pháp áp dụng:** Hoàn tất 100% chuẩn bị chứng chỉ ACM SSL (`us-east-1`) và bản ghi CNAME xác minh trên Route 53. Định tuyến lưu lượng trực tiếp về máy chủ EC2 qua Route 53 A Record trong thời gian chờ AWS Support mở khóa.

---

### 2. Định hướng Phát triển Kiến trúc Cloud Tương lai

#### Nâng cấp 2.1: Tự động hóa Hạ tầng bằng Infrastructure as Code (AWS CDK v2)
- Thay thế việc tạo tài nguyên thủ công bằng mã nguồn TypeScript sử dụng **AWS CDK v2**.
- Tự động hóa khởi tạo toàn bộ môi trường Cloud chỉ với 1 lệnh (`cdk deploy`).

#### Nâng cấp 2.2: Triển khai Đa vùng Active-Active (Multi-Region High Availability)
- Khởi tạo máy chủ EC2 tại các vùng phụ `ap-southeast-1` (Singapore) và `us-east-2` (Ohio).
- Tận dụng quy tắc Amazon ECR Cross-Region Replication và Route 53 Latency-Based Routing để tự động điều hướng người dùng tới máy chủ có độ trễ thấp nhất.

#### Nâng cấp 2.3: Nâng cấp Cơ sở dữ liệu Amazon Aurora Global Database
- Chuyển đổi từ RDS PostgreSQL sang Amazon Aurora PostgreSQL bằng dịch vụ AWS Database Migration Service (DMS).
- Cấu hình Aurora Global Database với Sydney làm Primary và các cluster Read-Replica tại Singapore và Ohio.

#### Nâng cấp 2.4: Tự động Mở rộng (Auto Scaling Group & Load Balancer)
- Thay thế EC2 đơn lẻ bằng nhóm Auto Scaling Group (ASG) đặt phía sau Application Load Balancer (ALB).
- Tự động tăng/giảm số lượng máy chủ EC2 dựa trên tải CPU thực tế và lưu lượng truy cập.

#### Nâng cấp 2.5: Giám sát Nâng cao CloudWatch Observability & Cảnh báo Tự động
- Cài đặt **CloudWatch Agent** trên EC2 để đo dung lượng RAM và Ổ cứng.
- Cấu hình **Route 53 Health Checks** giám sát trạng thái hoạt động của `https://trackermaint.dpdns.org`.
- Thiết lập **CloudWatch Alarms + Amazon SNS** gửi email cảnh báo tự động khi CPU > 80% hoặc đĩa cứng tràn.