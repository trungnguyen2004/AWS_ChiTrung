---
title: "Quản lý Log & Giám sát tập trung với CloudWatch"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

Trong bước này, bạn sẽ theo dõi chỉ số hiệu năng cơ sở dữ liệu thời gian thực và quản lý các nhóm nhật ký (Log Groups) trên Amazon CloudWatch.

---

### Bước 1: Theo dõi Giám sát Database Insights

1. Mở **Amazon CloudWatch Console** tại [https://console.aws.amazon.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/).
2. Trên menu bên trái dưới mục **Infrastructure Monitoring**, chọn **Database Insights**.
3. Chọn database instance: `tracker-maintenance-db`.
4. Xem các biểu đồ hiệu năng thời gian thực:
   - **Tải Database (Database Load - AAS):** Biểu đồ các phiên làm việc active
   - **Tải CPU & IO Waits:** Mức tiêu thụ phần cứng
   - **Top SQL Statements:** Phân tích danh sách các câu lệnh SQL thực thi chiếm nhiều tài nguyên nhất (`SELECT`, `INSERT`, `UPDATE`)

<div style="text-align: center; margin: 20px 0;">

  ![CloudWatch Database Insights](cloudwatch-db-insights.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.6.3. Giao diện CloudWatch Database Insights giám sát chỉ số tải CPU, active sessions và Top câu lệnh SQL của database.</div>
</div>

---

### Bước 2: Kiểm tra Quản lý Nhóm Log (Log Groups)

1. Trên menu bên trái CloudWatch Console dưới mục **Logs / Log management**, chọn **Log groups**.
2. Kiểm tra danh sách các nhóm log đang hoạt động:
   - **`RDSOSMetrics`**: Tự động ghi nhận chỉ số hệ điều hành của máy chủ RDS PostgreSQL (Lưu trữ 1 tháng).
   - **`/tracker-maintenance/backend`**: Đẩy log ứng dụng Spring Boot từ driver `awslogs` của Docker.
   - **`/tracker-maintenance/frontend`**: Đẩy log container Frontend React.

<div style="text-align: center; margin: 20px 0;">

  ![CloudWatch Log Groups](cloudwatch-log-groups.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Hình 5.6.4. Trang quản lý CloudWatch Log Groups hiển thị các nhóm log hạ tầng bao gồm RDSOSMetrics.</div>
</div>