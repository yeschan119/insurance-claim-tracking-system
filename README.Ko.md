# Insurance Claim Lifecycle Tracking System (보험 청구 라이프사이클 추적 시스템)

---
[English](README.md)

---

# 프로젝트 개요

이 프로젝트는 **Stedi EDI 플랫폼 기반 보험 청구 라이프사이클 추적 및 모니터링 시스템**이다.

Stedi는 의료 및 보험 산업에서 사용되는 **EDI (Electronic Data Interchange)** 트랜잭션을 API 기반으로 처리할 수 있도록 제공하는 클라우드 플랫폼이다.

본 시스템은 다음 EDI 트랜잭션을 통합한다.

- **837 (보험 청구 제출)**
- **277 (청구 상태 업데이트)**
- **835 (ERA 지급 정보)**

이를 하나의 라이프사이클 타임라인으로 구성하여 다음을 가능하게 한다.

- End-to-End 청구 추적
- 실시간 상태 가시성
- 지급 정산 관리
- 감사 대응 이력 관리

<img width="600" height="400" src="https://github.com/user-attachments/assets/579211a4-6e2f-4e39-acf7-12f46075ca86" />

---

# Tech Stack

### Backend

- Angular
- ASP.NET Core
- C#
- TypeScript
- Entity Framework Core
- AWS Lambda
- Amazon EventBridge

### Integration

- Stedi API
- X12 EDI (837 / 277 / 835)

### Database

- MySQL
- DynamoDB

---

# 프로젝트 구성

<details>
<summary>보험 라이프사이클 UI 구축</summary>

- Stedi API 연동
- 보험 청구 데이터 수집
- 다음 정보 UI 표시
  - 환자 정보
  - 보험사 정보
  - 청구 상태
  - 지급 내역
- 초기 데이터 파이프라인 설계
- UI를 위한 Backend API 구현

</details>

<details>
<summary>상태 동기화 전략</summary>

Stedi 특성상 모든 데이터가 완전한 실시간으로 반영되지 않기 때문에  
다음과 같은 **Hybrid 구조**를 설계하였다.

- Webhook 기반 실시간 반영
- 하루 1회 Batch 재동기화

이를 통해 **Eventual Consistency**를 보장하였다.

</details>

---

# 👨‍💻 나의 역할

<details>
<summary>초기 데이터 파이프라인 구축</summary>

- Stedi API 최초 연동
- 초기 대량 데이터 Bulk Insert 처리
- 837 / 277 / 835 상관관계 설계
- 라이프사이클 데이터 모델 설계
- Backend API 개발
- UI 대시보드 구현

<img width="800" height="500" src="https://github.com/user-attachments/assets/7af9580a-4a39-4847-931f-14f17f28370f" />

</details>

<details>
<summary>Batch 재동기화 로직</summary>

- 하루 1회 Batch 실행
- Watermark 기반 증분 수집
- Deduplication 정책
- Idempotent 저장 로직
- Exception Log Table 설계
- AWS Serverless 아키텍처 구현

</details>

---

# 성능 최적화

<details>
<summary>Watermark 기반 증분 처리</summary>

```
ProcessedAt > LastProcessedWatermark
```

- API 호출 감소
- 중복 처리 제거
- 성능 향상
- 비용 절감

</details>

<details>
<summary>200건 단위 Batch 처리</summary>

- 페이지 단위 처리
- 즉시 DB 반영
- 메모리 누적 방지
- 시스템 안정성 확보

</details>

---

# 기술적 의사결정

<details>
<summary>Serverless 아키텍처 선택</summary>

Event-driven Batch 구조이므로  
EC2 대신 **AWS Lambda 기반 Serverless 구조**를 선택하였다.

장점:

- 인프라 단순화
- 비용 효율
- 자동 확장
- 서버 관리 불필요

</details>

---

# 데이터 처리 흐름

<details>
<summary>실시간 처리 경로</summary>

- Webhook 수신
- 즉시 DB 반영
- 라이프사이클 갱신

</details>

<details>
<summary>Batch 처리 경로</summary>

- Stedi API 호출
- Watermark 필터링
- 중복 제거
- DB 업데이트

</details>

---

# 프로젝트 성과

- 대규모 트래픽 안정 처리
- 누락 데이터 제거
- 운영 리스크 감소
- 평균 4초 응답 시간 유지
- 1000+ 조직 지원

---

# 이 프로젝트가 보여주는 것

- Healthcare 데이터 파이프라인 설계
- Hybrid Event-driven + Batch 아키텍처
- Watermark 기반 증분 처리
- Write-optimized DB 설계
- Serverless 아키텍처 설계
