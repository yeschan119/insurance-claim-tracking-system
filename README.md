# Insurance Claim Lifecycle Tracking System

[한국어 🇰🇷](README.Ko.md)

---

# Project Overview

This project is a large-scale **Insurance Claim Lifecycle Tracking & Monitoring System** built on top of the **Stedi EDI platform**.

### What is Stedi?

**Stedi** is a cloud-based platform that enables organizations to process healthcare and insurance **EDI (Electronic Data Interchange)** transactions through modern APIs.

The system integrates:

- **837 (Claim Submission)**
- **277 (Claim Status Update)**
- **835 (ERA Payment)**

into a unified lifecycle timeline that enables:

- End-to-end claim traceability  
- Real-time status visibility  
- Accurate payment reconciliation  
- Audit-ready historical tracking  

<img width="600" height="400" alt="project3" src="https://github.com/user-attachments/assets/579211a4-6e2f-4e39-acf7-12f46075ca86" />

---

# Architecture Overview

```
Stedi API
    ↓
Initial Bulk Ingestion
    ↓
Database
    ↑
Webhook Listener → Real-Time Update
        ↑
EventBridge (Daily)
        ↓
Batch Lambda
        ↓
Reconciliation Logic
        ↓
Database
        ↓
Claim Lifecycle UI
```

Hybrid **Real-Time + Batch Processing Architecture**

---

# Tech Stack

### Backend
- Angular
- ASP.NET Core
- C#
- TypeScript
- HTML
- CSS
- Entity Framework Core
- AWS Lambda (.NET)
- Amazon EventBridge

### Integration
- Stedi API
- X12 EDI (837 / 277 / 835)

### Database
- MySQL
- DynamoDB
- Optimized index strategy
- Exception logging table

---

# Project Structure

<details>
<summary>Insurance Lifecycle Display (Stedi API Integration)</summary>

- Integrated Stedi APIs
- Retrieved insurance lifecycle data
- Displayed:
  - Patient information
  - Insurance provider data
  - Claim status updates
  - Payment details
- Designed and implemented initial data ingestion pipeline
- Built backend APIs for UI consumption

</details>

<details>
<summary>Status Synchronization Strategy</summary>

Because Stedi does not guarantee full real-time reflection of all transactions, we implemented a **hybrid architecture**:

- Webhook-based near real-time updates
- Daily batch reconciliation

This guarantees **eventual consistency** and prevents missing lifecycle updates.

</details>

---

# My Role

<details>
<summary>Initial Data Pipeline & UI Enablement</summary>

- Established first Stedi API integration
- Designed bulk ingestion logic
- Performed large-scale **bulk insert** for bootstrap data
- Built lifecycle correlation between **837 / 277 / 835**
- Implemented backend APIs powering the UI
- Delivered initial lifecycle dashboard

<img width="800" height="500" alt="EML" src="https://github.com/user-attachments/assets/7af9580a-4a39-4847-931f-14f17f28370f" />

After initial release:

- Teammates handled advanced filtering
- UI design improvements
- Additional front-end refinements

</details>

<details>
<summary>Daily Batch Reconciliation</summary>

I designed and implemented:

- Daily scheduled batch execution
- Watermark-based incremental fetch logic
- Deduplication policies
- Idempotent persistence logic
- Exception logging system
- Serverless execution pipeline

Ensured:

- No missing claim updates
- Automatic recovery from webhook failures
- Consistent lifecycle state integrity

</details>

---

# System Scale

| Metric | Value |
|------|------|
| Daily Batch Traffic | Hundreds ~ Thousands |
| Organizations | ~1000 |
| End Users | Hundreds of thousands |
| Average API Response Time | ~4 seconds |
| Architecture | Event-driven + Scheduled Batch |

Each transaction was processed independently to prevent cascading failures.

---

# Performance Optimization

<details>
<summary>Watermark-Based Incremental Fetch</summary>

Instead of retrieving the full transaction history every time:

```
ProcessedAt > LastProcessedWatermark
```

Benefits:

- Reduced API calls dramatically
- Eliminated duplicate processing
- Improved performance
- Lower infrastructure cost

</details>

<details>
<summary>Controlled Batch Processing</summary>

- Retrieved transactions in batches of **200**
- Processed per page
- Immediately persisted to database
- Prevented excessive memory accumulation
- Maintained system stability under load

</details>

<details>
<summary>Database Optimization</summary>

Since the system is write-heavy:

- Removed rarely used indexes
- Optimized insert/update execution paths
- Reduced index maintenance overhead
- Used batch-oriented database writes

</details>

---

# Technical Decisions

<details>
<summary>Serverless Architecture (Lambda over EC2)</summary>

Because this is an **event-driven batch system**, we chose **AWS Lambda**.

Advantages:

- Simpler infrastructure
- Cost efficiency
- Automatic scaling
- Built-in monitoring
- No server maintenance overhead

</details>

<details>
<summary>Exception Logging Strategy</summary>

To ensure no missing data:

- Created dedicated **exception log table**
- Logged:
  - Execution ID
  - Claim identifier
  - Error details
  - Timestamp

This enables safe **reprocessing and audit visibility**.

</details>

---

# Data Processing Flow

<details>
<summary>Real-Time Path</summary>

- Webhook receives **277 / 835 events**
- Immediate persistence
- Lifecycle timeline updated instantly

</details>

<details>
<summary>Batch Path (Daily)</summary>

- Fetch transactions from Stedi
- Apply watermark filtering
- Deduplicate records
- Insert/update database
- Update watermark

Ensures:

- Eventual consistency
- Recovery from missed webhook events
- Complete lifecycle tracking

</details>

---

# Impact

- Successfully handled high-volume claim traffic
- Eliminated missing lifecycle data
- Reduced operational risk
- Improved monitoring visibility
- Maintained consistent **~4 second response time**
- Scaled to support **1000+ organizations**

---

# What This Project Demonstrates

- Large-scale healthcare data ingestion design
- Hybrid real-time + batch architecture
- Watermark-based incremental processing
- Write-optimized database engineering
- Production-grade reliability design
- Serverless orchestration strategy
- Healthcare EDI domain expertise
