---
title: "Đóng gói Docker Multi-Stage & Docker Compose trên EC2"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

Trong bước này, bạn sẽ đóng gói ứng dụng Backend và Frontend thành các Docker Container tối ưu bộ nhớ RAM và thực thi điều phối bằng Docker Compose trên máy chủ EC2.

---

### Bước 1: Tạo `Dockerfile` Multi-Stage cho Backend

Tại thư mục gốc dự án Backend (`tracker_maintenance_service/`), tạo một file tên là `Dockerfile`. Sử dụng kỹ thuật Multi-Stage 2 giai đoạn để biên dịch file JAR Java 21 và đóng gói vào container Alpine siêu nhẹ:

```dockerfile
# Giai đoạn 1: Build ứng dụng Java 21 Spring Boot với Maven
FROM maven:3.9-amazoncorretto-21 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests

# Giai đoạn 2: Tạo container thực thi nhỏ gọn
FROM amazoncorretto:21-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8081
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

### Bước 2: Tạo `Dockerfile` cho Frontend

Tại thư mục gốc dự án Frontend (`tracker-fe/`), tạo file `Dockerfile`:

```dockerfile
# Build ứng dụng React Router chuẩn sản xuất
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Chạy ứng dụng web server
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app ./
EXPOSE 3000
CMD ["npm", "run", "start"]
```

---

### Bước 3: Tạo File Điều phối Container (`docker-compose.yml`)

Trên máy chủ EC2 (tại đường dẫn `/home/ec2-user/docker-compose.yml`), tạo file cấu hình điều phối. File này kéo trực tiếp Docker Image từ Amazon ECR và giới hạn bộ nhớ RAM để tránh tràn bộ nhớ (OOM) trên EC2 Free Tier:

```yaml
services:
  tracker-be:
    image: 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com/tracker-be:latest
    container_name: tracker-be
    ports:
      - "8081:8081"
    environment:
      JAVA_TOOL_OPTIONS: "-Xms256m -Xmx512m"
      DB_URL: jdbc:postgresql://tracker-maintenance-db.cvow26so4q44.ap-southeast-2.rds.amazonaws.com:5432/postgres
      DB_USERNAME: postgres
      DB_PASSWORD: YOUR_DB_PASSWORD
      AWS_S3_BUCKET: tracker-maintenance-images-123
    logging:
      driver: awslogs
      options:
        awslogs-region: ap-southeast-2
        awslogs-group: /tracker-maintenance/backend
        awslogs-create-group: "true"
        awslogs-stream: be-logs
    restart: always

  tracker-fe:
    image: 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com/tracker-fe:latest
    container_name: tracker-fe
    ports:
      - "3000:3000"
    environment:
      NODE_ENV: production
      VITE_API_URL: https://trackermaint.dpdns.org/api
    logging:
      driver: awslogs
      options:
        awslogs-region: ap-southeast-2
        awslogs-group: /tracker-maintenance/frontend
        awslogs-create-group: "true"
        awslogs-stream: fe-logs
    depends_on:
      - tracker-be
    restart: always
```

---

### Bước 4: Triển khai và Kiểm tra Trạng thái Container

1. Đăng nhập vào máy chủ EC2 qua SSH:
   ```bash
   ssh -i "your-key.pem" ec2-user@3.106.194.112
   ```
2. Đăng nhập Docker xác thực với Amazon ECR:
   ```bash
   aws ecr get-login-password --region ap-southeast-2 | docker login --username AWS --password-stdin 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com
   ```
3. Khởi chạy toàn bộ container ở chế độ chạy ngầm (detached mode):
   ```bash
   docker compose up -d
   ```
4. Kiểm tra trạng thái hoạt động của các container:
   ```bash
   docker ps
   ```
   **Kết quả mong đợi:** Bạn sẽ thấy cả 2 container `tracker-be` (cổng `8081`) và `tracker-fe` (cổng `3000`) đang ở trạng thái `Up`.