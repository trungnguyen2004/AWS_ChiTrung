---
title: "Centralized Logging & Monitoring with CloudWatch"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

In this step, monitor database performance telemetry and inspect system log groups in Amazon CloudWatch.

---

### Step 1: Inspect Database Insights Monitoring

1. Open the **Amazon CloudWatch Console** at [https://console.aws.amazon.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/).
2. In the left navigation pane under **Application Signals / Infrastructure Monitoring**, click **Database Insights** (or Performance Insights).
3. Select your database instance: `tracker-maintenance-db`.
4. Review real-time performance metrics:
   - **Database Load (AAS):** Active sessions timeline
   - **CPU Utilization & IO Waits:** Hardware resource consumption
   - **Top SQL Statements:** Analysis of top executing database queries (`SELECT`, `INSERT`, `UPDATE`)

<div style="text-align: center; margin: 20px 0;">

  ![CloudWatch Database Insights](cloudwatch-db-insights.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.6.3. CloudWatch Database Insights dashboard monitoring tracker-maintenance-db active sessions, CPU load, and Top SQL statements.</div>
</div>

---

### Step 2: Inspect CloudWatch Log Groups

1. In the CloudWatch Console left navigation pane under **Logs / Log management**, click **Log groups**.
2. Review active telemetry log groups:
   - **`RDSOSMetrics`**: Automatically streams OS-level metrics and telemetry for the PostgreSQL database (Retention: 1 month).
   - **`/tracker-maintenance/backend`**: Streams Spring Boot application logs from the `awslogs` Docker logging driver.
   - **`/tracker-maintenance/frontend`**: Streams React frontend container logs.

<div style="text-align: center; margin: 20px 0;">

  ![CloudWatch Log Groups](cloudwatch-log-groups.png?classes=shadow)

  <div style="font-weight: bold; margin-top: 8px; color: #555;">Figure 5.6.4. CloudWatch Log Groups management dashboard displaying RDSOSMetrics telemetry stream.</div>
</div>