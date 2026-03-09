# Insurance Claim Lifecycle Tracking System (보험 청구 라이프사이클 추적 시스템)

---
[English](README.md)

---

# 프로젝트 개요

이 프로젝트는 **Stedi EDI 플랫폼**을 기반으로 구축한  
**보험 청구 라이프사이클 추적 및 모니터링 시스템**이다.

---

## Stedi란?

**Stedi**는 의료 및 보험 산업에서 사용되는  
**EDI (Electronic Data Interchange)** 트랜잭션을  
API 기반으로 처리할 수 있도록 제공하는 클라우드 플랫폼이다.

본 시스템은 다음 EDI 트랜잭션을 통합한다.

- **837 (보험 청구 제출)**
- **277 (청구 상태 업데이트)**
- **835 (ERA 지급 정보)**

이 데이터를 하나의 **통합된 라이프사이클 타임라인**으로 구성하여 다음을 가능하게 한다.

- End-to-End 청구 추적성 확보  
- 실시간 상태 가시성 제공  
- 정확한 지급 정산 관리  
- 감사(Audit) 대응이 가능한 이력 관리  

<img width="600" height="400" alt="project3" src="https://github.com/user-attachments/assets/579211a4-6e2f-4e39-acf7-12f46075ca86" />

---

# 프로젝트 구성

본 프로젝트는 크게 두 가지 축으로 진행되었다.

---

## 1️⃣ Stedi API 기반 보험 라이프사이클 UI 구축

- Stedi API 연동
- 보험 청구 데이터 수집
- 다음 정보를 UI에 표시
  - 환자 정보
  - 보험사 정보
  - 청구 상태
  - 지급 내역
- 초기 대량 데이터 파이프라인 설계
- UI 제공을 위한 백엔드 API 구현

---

## 2️⃣ 상태 동기화 전략 설계

Stedi 특성상 모든 데이터가 완전한 실시간으로 반영되지 않기 때문에  
다음과 같은 **Hybrid 구조**를 설계하였다.

- Webhook 기반 실시간 반영
- 하루 1회 Batch 재동기화 (본인이 담당)

이를 통해 **Eventual Consistency**를 보장하고  
누락되는 데이터를 방지하였다.

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

# 👨‍💻 나의 역할

## 1️⃣ 초기 데이터 파이프라인 설계 및 UI 기반 구축

- Stedi API 최초 연동
- 초기 대량 데이터 Bulk Insert 처리
- 837 / 277 / 835 간 상관관계 매핑 설계
- 라이프사이클 통합 데이터 모델 설계
- UI를 위한 Backend API 개발
- 초기 라이프사이클 대시보드 구현

UI example

<img width="800" height="500" alt="EML" src="https://github.com/user-attachments/assets/7af9580a-4a39-4847-931f-14f17f28370f" />

이후 단계에서는

- 동료가 Filtering 기능 구현
- UI 디자인 개선
- 추가 Front-End 기능 확장

---

## 2️⃣ 일일 Batch 재동기화 로직 구현

다음 항목을 설계 및 구현하였다.

- 하루 1회 Batch 실행 구조
- Watermark 기반 증분 데이터 수집
- Deduplication 정책 설계
- Idempotent 저장 로직 구현
- Exception Log Table 구축
- AWS Serverless 아키텍처 설계

이를 통해 다음을 보장하였다.

- 누락 데이터 제거
- Webhook 실패 상황에서도 자동 복구
- 상태 데이터 정합성 유지

---

# 규모 (Scale)

| 항목 | 규모 |
|------|------|
| 평균 일일 배치 트래픽 | 수백 ~ 수천 건 |
| 조직 수 | 약 1000개 |
| 사용자 수 | 수십만 명 |
| 평균 API 응답 시간 | 약 4초 |
| 처리 구조 | Event-driven + Scheduled Batch |

각 요청은 **독립적으로 처리**되도록 설계하여  
장애 확산을 방지하였다.

---

# 성능 최적화 전략

## 1️⃣ Watermark 기반 증분 수집

매번 전체 데이터를 조회하지 않고 다음 조건으로 최신 데이터만 수집하였다.

```
ProcessedAt > LastProcessedWatermark
```

효과

- API 호출 횟수 감소
- 중복 처리 제거
- 처리 속도 향상
- 인프라 비용 절감

---

## 2️⃣ 200건 단위 Batch 처리

- 한 번에 200개씩 조회
- 페이지 단위 처리
- 즉시 DB 반영
- 메모리 누적 방지
- 시스템 안정성 확보

---

## 3️⃣ DB 최적화

Write-heavy 구조를 고려하여 다음과 같은 최적화를 적용하였다.

- 사용 빈도가 낮은 Index 제거
- Insert / Update 실행 경로 최적화
- Batch Insert 활용
- Index 유지 비용 감소

---

# ⚙ 기술적 의사결정

## Serverless (Lambda) 선택

Event-driven Batch 구조이므로  
EC2 대신 **AWS Lambda 기반 Serverless 구조**를 선택하였다.

장점

- 인프라 단순화
- 비용 효율성
- 자동 확장
- 모니터링 용이
- 서버 관리 부담 제거

---

## Exception Log Table 설계

누락 데이터 방지를 위해 별도의 **Exception Log Table**을 설계하였다.

기록 항목

- Execution ID
- Claim Identifier
- Error Details
- Timestamp

이를 통해 **재처리 및 감사 대응**이 가능하도록 하였다.

---

# 아키텍처 개요

## Hybrid 실시간 + Batch 구조

```
Stedi API
    ↓
초기 Bulk Ingestion
    ↓
Database
    ↑
Webhook Listener → 실시간 업데이트
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

---

# 🔄 데이터 처리 흐름

## 실시간 경로

- Webhook으로 277 / 835 이벤트 수신
- 즉시 DB 반영
- 라이프사이클 타임라인 갱신

## Batch 경로 (하루 1회)

- Stedi API 호출
- Watermark 기준 필터링
- 중복 제거
- DB 저장
- Watermark 업데이트

보장 사항

- Eventual Consistency
- 누락 이벤트 복구
- 완전한 라이프사이클 추적

---

# 프로젝트 성과 (Impact)

- 대규모 트래픽 안정적 처리
- 누락 데이터 제거
- 운영 리스크 감소
- 모니터링 가시성 향상
- 평균 약 **4초 응답 시간 유지**
- **1000개 이상의 조직**을 안정적으로 지원

---

# 이 프로젝트가 보여주는 것

- 대규모 의료 데이터 파이프라인 설계 역량
- Hybrid Event-driven + Batch 아키텍처 설계 경험
- Watermark 기반 증분 데이터 처리 전략
- Write-optimized 데이터베이스 설계
- Serverless 인프라 설계 능력
- Healthcare EDI 도메인 이해
- 운영 안정성을 고려한 시스템 설계
