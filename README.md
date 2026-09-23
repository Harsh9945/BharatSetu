# BharatSetu (भारतसेतु)

> **Status:** 🏛️ Proposal & Architecture Phase  
> **Challenge:** Drunix Hackathon in collaboration with Citi  
> **Problem Statement:** Real-Time Payments  
> **Repository:** [https://github.com/Harsh9945/BharatSetu](https://github.com/Harsh9945/BharatSetu)

---

## 📌 Overview

**BharatSetu** is a proposed permissioned, cross-organization **Trusted Retry Ledger** for real-time payments, built on Drunix.

The system explores how merchant PSPs, issuing banks, and other authorized payment participants can independently verify whether a failed transaction is eligible for controlled recovery — without storing, exposing, or replaying sensitive authentication credentials such as PINs, OTPs, or CVVs.

Real-time payments can fail because of transient technical conditions such as network timeouts, gateway errors, and payment-switch disruptions. These **soft failures** are different from genuine declines, but recovering them safely across organizational boundaries requires more than a local retry flag or cache.

BharatSetu addresses this cross-organization trust problem by combining:

- AI-based payment failure classification
- Risk and Expected Value (EV) based retry decisioning
- A permissioned Drunix consent-proof ledger
- Time-bound and single-use retry authorization
- Cross-organization verification and auditability

---

## 🧩 Problem

Transient technical failures can cause otherwise recoverable payment attempts to fail.

The challenge is not to store or replay sensitive credentials. Instead, the system needs a way to establish a **secure, time-bound, independently verifiable proof of prior authentication and retry eligibility** that authorized organizations can verify without relying entirely on another organization's internal state.

Without such a shared trust mechanism, autonomous recovery across organizational boundaries becomes difficult to coordinate safely.

BharatSetu explores whether a permissioned ledger can provide this shared trust layer for real-time payment recovery.

---

## 💡 Solution

BharatSetu combines three coordinated layers:

### 1. AI-Based Failure Classification & Decisioning

A failure-classification service distinguishes between:

- **Soft failures** — transient network, gateway, or switch-related failures
- **Hard failures** — insufficient funds, invalid credentials, genuine declines, and other non-retryable conditions

Eligible soft failures are evaluated by a risk and Expected Value (EV) decision layer using configurable controls such as:

- Maximum retry attempts
- Cooldown periods
- Transaction-value thresholds
- Risk conditions
- Manual-review escalation

---

### 2. Trusted Consent Proof on Drunix

When the original payment authentication succeeds, BharatSetu creates a **cryptographically verifiable, time-bound and single-use consent proof** associated with that transaction.

The system does **not** store or replay:

- PINs
- OTPs
- CVVs
- Authentication secrets

The proof represents the relevant authorization context required for the recovery workflow.

The proof lifecycle is maintained through the permissioned Drunix network so authorized participants can independently verify its state.

---

### 3. Verified & Controlled Retry

A retry is permitted only when:

1. The AI/risk layer classifies the failure as eligible.
2. The retry policy conditions are satisfied.
3. The consent proof is valid.
4. The proof has not expired.
5. The proof has not already been redeemed.
6. The proof corresponds to the intended transaction.

After the proof is consumed, its lifecycle state is updated on the ledger, preventing reuse of the same authorization artifact.

This creates a **shared, tamper-evident trust mechanism for payment recovery across participating organizations**.

---

## 🔄 End-to-End Flow

```text
Original Payment
       │
       ▼
Customer Authentication
       │
       ▼
Consent Proof Issued
       │
       ▼
Payment Attempt
       │
 ┌─────┴─────┐
 ▼           ▼
SUCCESS    FAILURE
             │
             ▼
      AI Failure Classifier
             │
      ┌──────┴──────┐
      ▼             ▼
  HARD FAIL      SOFT FAIL
      │             │
      ▼             ▼
Re-auth Flow     EV / Risk Gate
                    │
             ┌──────┴──────┐
             ▼             ▼
           BLOCK          RETRY
                           │
                           ▼
                  Verify Drunix Proof
                           │
                    ┌──────┴──────┐
                    ▼             ▼
                 INVALID        VALID
                    │             │
                  BLOCK           ▼
                              Controlled
                                Retry
                                  │
                                  ▼
                            Redeem Proof
```

---

## 🏗️ Repository Structure

```text
bharatsetu/
├── README.md                 ← Project overview, proposal & end-to-end flow
├── docs/
│   └── architecture.md       ← Complete architecture diagram, component split & tech stack
├── chaincode/
│   └── .gitkeep              ← Java chaincode for Drunix consent proof ledger
├── backend/
│   └── .gitkeep              ← Spring Boot orchestrator & FastAPI AI engine
└── frontend/
    └── .gitkeep              ← React client web interface
```

---

## 🛠️ Technology Stack Overview

- **Trust Layer / Ledger:** Drunix — permissioned distributed-ledger platform with **Java chaincode**
- **Decision Layer:** FastAPI (Python) — AI failure classifier & EV/Risk gate
- **Orchestration:** Spring Boot
- **Streaming / State:** Apache Kafka, Redis
- **Persistence:** PostgreSQL
- **Frontend:** React
- **Infrastructure:** Docker, Docker Compose (Drunix sample/test network)

---

## 📜 License & Acknowledgments

Built for the **Drunix Hackathon in collaboration with Citi** | [Repository](https://github.com/Harsh9945/BharatSetu)
