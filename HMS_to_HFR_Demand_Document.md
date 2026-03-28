# HMS → HFR Integration — Demand & Estimation Document
### Prepared for Client Review | Version 1.0 | March 2026

---

> **Important Disclaimer**
> This document is an **indicative estimate** prepared at an early stage of the engagement. Several architectural and business decisions are still pending (see Section 6 — Open Unknowns). All figures — effort, cost, and timeline — are based on current understanding and carry a **±25% variance**. A revised and firmed estimate will be issued after the Discovery Phase is complete.

---

## 1. Executive Summary

This document outlines the estimated scope, team, timeline, and cost for building an end-to-end data integration pipeline on **AWS** that extracts facility data from the **FundooHMS (Hospital Management System)**, transforms it, and pushes it to the **Ayushman Bharat Digital Mission (ABDM) Health Facility Registry (HFR)** via REST APIs.

| Parameter | Estimate |
|---|---|
| **Total Project Duration** | 20 – 24 weeks (5 – 6 months) |
| **Team Size** | 5 – 6 people |
| **Total Delivery Cost (One-Time)** | $85,000 – $1,15,000 |
| **AWS Infrastructure Cost (Monthly, Production)** | $200 – $380 / month |
| **AWS Infrastructure Cost (Annual, Production)** | $2,400 – $4,560 / year |
| **Post Go-Live Support (Optional, Monthly)** | $3,000 – $5,000 / month |

---

## 2. Project Scope

### 2.1 In Scope
- Design and provisioning of AWS infrastructure (ground-up, from scratch)
- Flexible dual-path ingestion layer (MySQL DB direct + HMS API pull)
- S3-based 3-zone data lake (Raw → Processed → Ready → Error)
- AWS Glue ETL jobs for data cleaning, deduplication, column derivation, LGD code mapping, and HFR schema mapping
- AWS Step Functions orchestration (batch + near-real-time capable)
- HFR API integration — Search (dedup check) + Create / Update facility
- Error handling: SQS retry queue, error zone, CloudWatch alerting via SNS
- Security baseline: VPC, IAM roles, Secrets Manager for HFR auth tokens
- Documentation: Architecture, runbook, deployment guide

### 2.2 Out of Scope
- Changes or modifications to the HMS source system
- HFR sandbox access procurement (client responsibility)
- LGD code master data sourcing (client responsibility)
- Ongoing BAU operations post go-live (covered under optional support)
- Mobile or UI development of any kind
- Compliance certifications (HIPAA, ISO, etc.)

---

## 3. Assumptions

The following assumptions have been made in preparing this estimate. Any deviation will require a scope and cost review.

| # | Assumption |
|---|---|
| A1 | HMS MySQL database access or HMS REST APIs will be made available to the integration team |
| A2 | HFR sandbox credentials will be provided by the client within 2 weeks of project kick-off |
| A3 | LGD code master reference data will be provided by the client |
| A4 | The initial load volume is in the range of 5,000 – 10,000 facility records |
| A5 | The pipeline will operate as a scheduled batch (daily or hourly) at go-live; real-time is a future phase |
| A6 | Client will provide a dedicated AWS account or a sub-account for this project |
| A7 | A single AWS region will be used (e.g., ap-south-1 — Mumbai) |
| A8 | No pre-existing AWS infrastructure is in place |
| A9 | Client stakeholders will be available for weekly review calls and timely feedback |
| A10 | HFR API rate limits and sandbox behaviour are consistent with production |

---

## 4. Team Composition & Effort Estimate

### 4.1 Recommended Team

| Role | Responsibility | FTE Allocation | Duration |
|---|---|---|---|
| **Project Manager / Delivery Lead** | Delivery oversight, client communication, risk management, status reporting | 0.5 FTE | Full project (20–24 weeks) |
| **AWS Solution Architect** | Infrastructure design, AWS account setup, VPC, IAM, architecture decisions | 1.0 FTE (Phase 0–1) → 0.25 FTE advisory | ~10 weeks full + advisory |
| **Senior Data Engineer** | Glue ETL jobs, transformation logic, S3 data lake setup, deduplication | 1.0 FTE | Full project |
| **Data Engineer** | Supporting ETL development, LGD code mapping, schema mapping, testing | 1.0 FTE | Phases 2–4 (14–16 weeks) |
| **Backend / Integration Developer** | Lambda development, HFR API integration, SQS retry logic | 1.0 FTE | Phases 2–4 (14–16 weeks) |
| **DevOps / Cloud Engineer** | IaC (Terraform/CDK), CI/CD pipeline, CloudWatch dashboards, deployment | 1.0 FTE (Phase 1) → 0.5 FTE | ~8 weeks full + part-time |
| **QA / Test Engineer** | Test planning, integration testing, UAT support, regression | 1.0 FTE | Phase 4–5 (6–8 weeks) |

> **Minimum viable team**: 5 people (SA doubles as DevOps, one DE handles both data and testing in early phases). Recommended team is 6 for comfortable delivery with buffer.

---

### 4.2 Effort by Phase (Person-Weeks)

| Phase | PM | SA | Sr. DE | DE | Backend Dev | DevOps | QA | Total Person-Weeks |
|---|---|---|---|---|---|---|---|---|
| Phase 0: Discovery | 1.5 | 3 | 1 | — | — | — | — | 5.5 |
| Phase 1: Infrastructure Setup | 1 | 3 | 1 | — | — | 4 | — | 9 |
| Phase 2: Ingestion Layer | 1 | 1 | 3 | 2 | 1 | 1 | — | 9 |
| Phase 3: Transformation Layer | 1 | 0.5 | 5 | 4 | 2 | 0.5 | 1 | 14 |
| Phase 4: HFR Integration Layer | 1 | 0.5 | 2 | 2 | 5 | 0.5 | 1 | 12 |
| Phase 5: Testing & UAT | 1.5 | 0.5 | 2 | 2 | 2 | 0.5 | 5 | 13.5 |
| Phase 6: Go-Live & Handover | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 7 |
| **Total Person-Weeks** | **8** | **9.5** | **15** | **11** | **11** | **7.5** | **8** | **70** |

---

## 5. Project Timeline

> Timeline assumes project kick-off within 1 week of contract signing and HFR sandbox access within 2 weeks of kick-off.

```
Week:       1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17  18  19  20  21  22  23  24
            |---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|

Phase 0     [===Discovery & Req. Finalization===]
Phase 1             [=======AWS Infrastructure Setup========]
Phase 2                     [===Ingestion Layer (DB + API)======]
Phase 3                                 [=======Transformation Layer (Glue ETL)==========]
Phase 4                                             [======HFR Integration Layer=========]
Phase 5                                                         [=====Testing & UAT======]
Phase 6                                                                     [==Go-Live & Handover==]
```

### Phase Breakdown

| Phase | Duration | Key Deliverables |
|---|---|---|
| **Phase 0** — Discovery & Requirement Finalization | 3 weeks | Confirmed ingestion path, field mapping document, LGD code mapping, HFR sandbox access, finalised architecture |
| **Phase 1** — AWS Infrastructure Setup | 4 weeks | VPC, S3 buckets, IAM roles, Secrets Manager, EventBridge, Step Functions skeleton, IaC (Terraform/CDK) |
| **Phase 2** — Ingestion Layer | 4 weeks | DMS/Glue connector (DB path) + Lambda API puller — both landing to S3 Raw Zone |
| **Phase 3** — Transformation Layer | 6 weeks | Glue ETL jobs: cleaning, dedup, column derivation, LGD mapping, HFR schema mapping, validation |
| **Phase 4** — HFR Integration Layer | 5 weeks | Search Lambda + Create/Update Lambda, SQS retry, error zone, end-to-end flow |
| **Phase 5** — Testing & UAT | 5 weeks | Integration testing, performance testing (10K records), UAT with client, defect fixing |
| **Phase 6** — Go-Live & Handover | 3 weeks | Production deployment, runbook, monitoring dashboards, knowledge transfer |

### Timeline Risk Buffer
Given the number of open unknowns (Section 6), a **3–4 week contingency buffer** is built into the 24-week estimate. If major unknowns are resolved in Phase 0, delivery may complete closer to **20 weeks**.

---

## 6. Open Unknowns — Impact on Timeline & Cost

The items below are currently unresolved and may directly affect scope, effort, and cost. Each will be addressed during Phase 0 (Discovery).

| # | Unknown | Potential Impact |
|---|---|---|
| U1 | **HMS access method** — DB direct, API only, or both? | Affects ingestion layer complexity. Both paths = +2 weeks effort |
| U2 | **Batch vs near-real-time** — If real-time is required at go-live, Kinesis Data Streams may be needed | +3–4 weeks + ~$150–200/month additional AWS cost |
| U3 | **LGD code mapping complexity** — How clean and complete is location data in HMS? | Could add 1–2 weeks to Glue ETL development |
| U4 | **HFR sandbox availability** — Delays in sandbox access block integration development | Each week of delay = 1 week of idle backend dev time |
| U5 | **HFR API rate limits** — Unknown throttling behaviour in production | May require batching logic + backoff; +1 week |
| U6 | **Data quality of HMS source** — Volume of dirty / incomplete records unknown until profiling | Could extend transformation phase by 2–3 weeks |
| U7 | **One-time bulk load vs ongoing delta** — Is this a one-time migration or continuous sync? | Ongoing sync requires CDC setup and delta detection logic; +2–3 weeks |

---

## 7. AWS Infrastructure Cost Estimate

### 7.1 Monthly Cost — Production Environment (Steady State)

| AWS Service | Usage Assumption | Monthly Cost (USD) |
|---|---|---|
| **Amazon S3** | 4 zones × ~10 GB storage + request costs | $5 – $15 |
| **AWS Glue** | 2 DPUs × 1 hr × 30 runs/month (daily batch) | $26 – $60 |
| **AWS Lambda** | 2 functions × 10K invocations × ~3s avg | $5 – $15 |
| **AWS DMS** | t3.medium (if DB replication path is used) | $80 – $120 |
| **AWS Step Functions** | Standard workflow, ~50K state transitions/month | $2 – $5 |
| **Amazon SQS** | <1M requests/month | $1 – $5 |
| **Amazon SNS** | <1M notifications/month | $1 – $3 |
| **Amazon CloudWatch** | Logs ingestion + custom metrics + 1 dashboard | $20 – $40 |
| **AWS Secrets Manager** | 5 secrets | $2 – $5 |
| **VPC + NAT Gateway** | 1 NAT Gateway, low data transfer | $30 – $50 |
| **Amazon EventBridge** | Scheduler rules + events | $1 – $5 |
| **AWS IAM / KMS** | Key management for encryption | $5 – $10 |
| **Misc. (Data Transfer, etc.)** | Outbound to HFR APIs | $5 – $15 |
| **Subtotal** | | **$183 – $348 / month** |
| **Recommended Budget** | | **~$300 – $380 / month** |

> **Note**: DMS ($80–$120/month) is only applicable if the DB direct path is chosen. If only the API path is used, monthly cost drops to approximately **$100–$200/month**.

### 7.2 Monthly Cost — Development / Staging Environment

| Environment | Monthly Cost |
|---|---|
| Development | $80 – $130 / month |
| Staging / UAT | $100 – $160 / month |

> Development and staging environments run on smaller instance types and lower usage patterns.

### 7.3 One-Time / Setup Costs (AWS)

| Item | Estimated Cost |
|---|---|
| Initial bulk data load (higher Glue DPU for one-time run) | $50 – $150 |
| AWS account setup, VPC, baseline config | Included in delivery cost |
| **Total One-Time AWS Cost** | **$50 – $150** |

### 7.4 Annual AWS Cost Summary

| Environment | Monthly | Annual |
|---|---|---|
| Production | $300 – $380 | **$3,600 – $4,560** |
| Development + Staging (project duration ~6 months) | $180 – $290 | **$1,080 – $1,740** (6 months) |
| **Total AWS Cost (Year 1)** | | **$4,680 – $6,300** |

---

## 8. Delivery Cost Estimate

### 8.1 Rate Card (Blended, Offshore)

| Role | Day Rate (USD) | Month Rate (USD) |
|---|---|---|
| Project Manager / Delivery Lead | $350 – $450 | $7,000 – $9,000 |
| AWS Solution Architect | $550 – $700 | $11,000 – $14,000 |
| Senior Data Engineer | $450 – $600 | $9,000 – $12,000 |
| Data Engineer | $300 – $450 | $6,000 – $9,000 |
| Backend / Integration Developer | $350 – $500 | $7,000 – $10,000 |
| DevOps / Cloud Engineer | $400 – $550 | $8,000 – $11,000 |
| QA / Test Engineer | $250 – $380 | $5,000 – $7,600 |

### 8.2 Total Delivery Cost by Phase

| Phase | Duration | Approx. Delivery Cost (USD) |
|---|---|---|
| Phase 0 — Discovery | 3 weeks | $8,000 – $12,000 |
| Phase 1 — Infrastructure Setup | 4 weeks | $14,000 – $20,000 |
| Phase 2 — Ingestion Layer | 4 weeks | $14,000 – $20,000 |
| Phase 3 — Transformation Layer | 6 weeks | $18,000 – $26,000 |
| Phase 4 — HFR Integration | 5 weeks | $16,000 – $22,000 |
| Phase 5 — Testing & UAT | 5 weeks | $12,000 – $18,000 |
| Phase 6 — Go-Live & Handover | 3 weeks | $8,000 – $12,000 |
| **Total Delivery Cost** | **~20–24 weeks** | **$90,000 – $1,30,000** |
| **Recommended Budget (with 15% contingency)** | | **~$1,00,000 – $1,50,000** |

> Contingency of 15% is included to account for open unknowns (Section 6) that may require additional effort once resolved in Discovery.

---

## 9. Total Cost of Ownership (TCO) — Year 1

| Cost Category | Amount (USD) |
|---|---|
| One-Time Delivery Cost | $90,000 – $1,30,000 |
| AWS Infrastructure — Year 1 (Prod + Dev/Staging) | $4,680 – $6,300 |
| **Total Year 1 Cost** | **$94,680 – $1,36,300** |

| Ongoing Annual Cost (Year 2+) | Amount (USD) |
|---|---|
| AWS Infrastructure (Production only) | $3,600 – $4,560 / year |
| Optional BAU Support | $36,000 – $60,000 / year |

---

## 10. Optional: Post Go-Live Support

| Support Tier | Coverage | Monthly Cost |
|---|---|---|
| **Basic** | Bug fixes, monitoring, minor config changes | $2,500 – $3,500 / month |
| **Standard** | Basic + feature enhancements (up to 20 hrs/month) | $4,000 – $6,000 / month |
| **Premium** | Standard + dedicated on-call, SLA-backed response | $7,000 – $10,000 / month |

---

## 11. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| HFR sandbox access delayed beyond 2 weeks | High | High | Begin transformation work in parallel; mock HFR API responses for local testing |
| HMS data quality is significantly worse than expected | Medium | High | Dedicated data profiling sprint in Phase 0; budget contingency covers extra cleansing work |
| Real-time requirement added post kick-off | Medium | Medium | Architecture is designed to support this; adds ~3–4 weeks and Kinesis cost |
| HFR API rate limiting blocks bulk push | Medium | Medium | Implement exponential backoff + batch size controls in Lambda from the start |
| Unclear LGD code mapping logic | High | Medium | Raise with client in Phase 0; may require a reference data workshop |
| AWS account procurement delays | Low | High | Flag early; advise client to initiate AWS account setup before project start |

---

## 12. Payment Milestones (Suggested)

| Milestone | % of Total | Trigger |
|---|---|---|
| Project Kick-off | 20% | Contract signed |
| Phase 0 Complete — Discovery Sign-off | 10% | Architecture & field mapping approved by client |
| Phase 1–2 Complete — Infrastructure + Ingestion live | 20% | Data flowing into S3 Raw Zone confirmed |
| Phase 3 Complete — Transformation Layer complete | 20% | Glue jobs processing and writing to Ready Zone |
| Phase 4–5 Complete — UAT Sign-off | 20% | Client UAT sign-off received |
| Phase 6 Complete — Go-Live | 10% | Production deployment confirmed |

---

## 13. Next Steps to Firm Up This Estimate

The following actions, if completed before or during Phase 0, will significantly reduce variance in this estimate:

1. **Confirm HMS access method** (DB / API / both)
2. **Confirm go-live trigger type** (batch schedule or near-real-time)
3. **Share a sample of HMS facility data** (even 50–100 rows) for data profiling
4. **Confirm HFR sandbox access availability**
5. **Provide LGD code master reference** or confirm availability
6. **Confirm AWS account status** — new account or existing org sub-account
7. **Agree on Discovery Phase scope and kick-off date**

---

## 14. Document Sign-off

| Role | Name | Signature | Date |
|---|---|---|---|
| Client — Business Owner | | | |
| Client — Technical Lead | | | |
| Delivery Lead | | | |
| Solution Architect | | | |

---

*This document is confidential and prepared exclusively for the client's internal review and decision-making. All estimates are indicative and subject to revision following the Discovery Phase.*

*Prepared by: Delivery Team | Version 1.0 | March 2026*
