---
title: "Multi-Stage Docker & Remote Docker Compose Setup"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

In this step, package the application services into multi-stage Docker containers and orchestrate them with Docker Compose on the EC2 server.

---

### Step 1: Create Backend Multi-Stage `Dockerfile`

In your backend project root (`tracker_maintenance_service/`), create a file named `Dockerfile`. This uses a 2-stage build to compile the Java 21 Spring Boot JAR and package it into a minimal Alpine container:

```dockerfile
# Stage 1: Build Java 21 Spring Boot application with Maven
FROM maven:3.9-amazoncorretto-21 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests

# Stage 2: Create lightweight execution container
FROM amazoncorretto:21-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8081
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

### Step 2: Create Frontend `Dockerfile`

In your frontend React application directory (`tracker-fe/`), create a file named `Dockerfile`:

```dockerfile
# Build React Router application for production
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Run application server
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app ./
EXPOSE 3000
CMD ["npm", "run", "start"]
```

---

### Step 3: Create Remote Orchestration File (`docker-compose.yml`)

On the EC2 server (in `/home/ec2-user/docker-compose.yml`), create the container orchestration configuration file. This pulls Docker images directly from Amazon ECR and sets memory limits to prevent out-of-memory crashes on EC2 Free Tier:

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

### Step 4: Deploy and Verify Running Containers

1. Log into your EC2 server via SSH:
   ```bash
   ssh -i "your-key.pem" ec2-user@3.106.194.112
   ```
2. Authenticate Docker with Amazon ECR:
   ```bash
   aws ecr get-login-password --region ap-southeast-2 | docker login --username AWS --password-stdin 534120921488.dkr.ecr.ap-southeast-2.amazonaws.com
   ```
3. Launch the container stack in detached mode:
   ```bash
   docker compose up -d
   ```
4. Verify that both containers are running in healthy status:
   ```bash
   docker ps
   ```
   **Expected Output:** You should see `tracker-be` running on port `8081` and `tracker-fe` running on port `3000` with status `Up`.