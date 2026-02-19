# Insurance Claim Lifecycle Tracking System

[한국어 🇰🇷](README.Ko.md)

---

## Project Overview
This project is a large-scale **Insurance Claim Lifecycle Tracking & Monitoring System** built on top of the Stedi EDI platform.

## 📌 What is Stedi?
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

---

## Project Structure

This project was executed in two major tracks:

### 1️⃣ Insurance Lifecycle Display (Stedi API Integration) - my responsibility

- Integrated Stedi APIs
- Retrieved insurance lifecycle data
- Displayed:
  - Patient information
  - Insurance provider data
  - Claim status updates
  - Payment details
- Designed and implemented initial data ingestion pipeline
- Built backend APIs for UI consumption

---

### 2️⃣ Status Synchronization Strategy

Because Stedi does not guarantee full real-time reflection of all transactions, we implemented a **hybrid architecture**:

- Webhook-based near real-time updates
- Daily batch reconciliation (my responsibility)

This guarantees **eventual consistency** and prevents missing lifecycle updates.

---

# My Role

## 1️⃣ Initial Data Pipeline & UI Enablement

- Established first Stedi API integration
- Designed bulk ingestion logic
- Performed large-scale **bulk insert** for bootstrap data
- Built lifecycle correlation between 837 / 277 / 835
- Implemented backend APIs powering the UI
- Delivered initial lifecycle dashboard
- UI Example

<img width="800" height="500" alt="EML" src="https://github.com/user-attachments/assets/7af9580a-4a39-4847-931f-14f17f28370f" />

After initial release:
- Teammates handled advanced filtering
- UI design improvements
- Additional front-end refinements

---

## 2️⃣ Daily Batch Reconciliation

I designed and implemented:

- Daily scheduled batch execution
- Watermark-based incremental fetch logic
- Deduplication policies
- Idempotent persistence logic
- Exception logging system
- Serverless execution pipeline

This ensured:

- No missing claim updates
- Automatic recovery from webhook failures
- Consistent lifecycle state integrity

---

# Scale

| Metric | Value |
|--------|--------|
| Daily Batch Traffic | Hundreds ~ Thousands |
| Organizations | ~1000 |
| End Users (Patients & Staff) | Hundreds of thousands |
| Average API Response Time | ~4 seconds |
| Architecture | Event-driven + Scheduled Batch |

Each transaction was processed independently to prevent cascading failures.

---

# Performance Optimization

## Watermark-Based Incremental Fetch

Instead of retrieving the full transaction history every time:

Fetch only transactions where:

ProcessedAt > LastProcessedWatermark

Benefits:

- Reduced API calls dramatically
- Eliminated duplicate processing
- Improved performance
- Lower infrastructure cost

---

## Controlled Batch Processing

- Retrieved transactions in batches of 200
- Processed per page
- Immediately persisted to database
- Prevented excessive memory accumulation
- Maintained system stability under load

---

## Database Optimization

Since the system is write-heavy:

- Removed rarely used indexes
- Optimized insert/update execution paths
- Reduced index maintenance overhead
- Used batch-oriented database writes

---

# Technical Decisions

## Serverless over EC2

Because this is an event-driven batch system, we chose **AWS Lambda** instead of EC2.

Advantages:

- Simpler infrastructure
- Cost efficiency
- Automatic scaling
- Built-in monitoring
- No server maintenance overhead

---

## Exception Logging Strategy

To ensure no missing data:

- Created dedicated exception log table
- Logged:
  - Execution ID
  - Claim identifier
  - Error details
  - Timestamp
- Enabled safe reprocessing and audit visibility

---

# Architecture Overview

## Hybrid Real-Time + Batch Model

    Stedi API
        ↓
    Initial Bulk Ingestion
        ↓
    Database
        ↑
    Webhook Listener  → Real-Time Update
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

---

# Data Processing Flow

## Real-Time Path

- Webhook receives 277 / 835 events
- Immediate persistence
- Lifecycle timeline updated instantly

## Batch Path (Daily)

- Fetch transactions from Stedi
- Apply watermark filtering
- Deduplicate records
- Insert/update database
- Update watermark

Ensures:

- Eventual consistency
- Recovery from missed webhook events
- Complete lifecycle tracking

---

# Impact

- Successfully handled high-volume claim traffic
- Eliminated missing lifecycle data
- Reduced operational risk
- Improved monitoring visibility
- Maintained consistent ~4 second response time
- Scaled to support 1000+ organizations

---

# Tech Stack

### Backend
- ASP.NET Core
- C#
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

# What This Project Demonstrates

- Large-scale healthcare data ingestion design
- Hybrid real-time + batch architecture
- Watermark-based incremental processing
- Write-optimized database engineering
- Production-grade reliability design
- Serverless orchestration strategy
- Healthcare EDI domain expertise
