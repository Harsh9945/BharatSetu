# BharatSetu Architecture Specification

> **Document Version:** 1.0.0 (Proposal Stage)  
> **Target Repository:** [BharatSetu](https://github.com/Harsh9945/BharatSetu)

---

## 📌 Executive Summary

BharatSetu is designed as a resilient, decoupled civic infrastructure and trade orchestration platform. The architecture ensures that user requests (grievances, trade transactions, service applications) are enriched, routed, persistently logged, and verified via decentralized smart contracts without relying on transient single-session states.

---

## 🔄 Architectural Flow Diagram

```mermaid
flowchart TD
    subgraph ClientLayer["1. Citizen & Admin Client Plane (frontend/)"]
        CitizenUI["Citizen Portal (Chat / Voice / Web)"]
        GovUI["Government / Admin Console"]
    end

    subgraph ServiceLayer["2. Backend & Gateway Layer (backend/)"]
        APIGateway["API Gateway & Router"]
        Orchestrator["Workflow Orchestrator"]
        WorkerQueue["Async Task Workers"]
    end

    subgraph LedgerLayer["3. Ledger & Smart Contract Layer (chaincode/)"]
        SmartContract["Chaincode Logic (State Machine)"]
        LedgerState["Immutable State Ledger"]
    end

    subgraph StorageLayer["4. Persistence & External Services"]
        Database["Persistence Store (Cosmos DB / Postgres)"]
        AIServices["AI & Language Services (Translation / Speech)"]
    end

    %% Interaction Flow
    CitizenUI -->|"1. Submit Request / Grievance"| APIGateway
    GovUI -->|"Oversight & Action"| APIGateway

    APIGateway -->|"2. Process & Translate"| AIServices
    APIGateway -->|"3. Queue Async Task"| WorkerQueue
    WorkerQueue -->|"4. Route & Enforce Workflow"| Orchestrator

    Orchestrator -->|"5. Persist Application State"| Database
    Orchestrator -->|"6. Commit Transaction"| SmartContract

    SmartContract -->|"7. Update Ledger"| LedgerState
    LedgerState -->|"8. Event Notification"| APIGateway
    APIGateway -->|"9. Real-time Status Update"| CitizenUI
    APIGateway -->|"10. Update Dashboard"| GovUI
```

---

## 🧱 Component Architecture Table

| Component Layer | Directory | Key Elements | Responsibility & Description |
| :--- | :--- | :--- | :--- |
| **Citizen & Admin Plane** | `frontend/` | Next.js Client, Web & Mobile UI, Voice/Chat Ingestion | User-facing portal for submitting requests, filing grievances, uploading documents, and real-time tracking. Includes administrator oversight dashboard. |
| **Backend & Routing Gateway** | `backend/` | API Gateway, Routing Engine, Async Worker Queues | Ingests requests, performs authentication, manages background tasks, orchestrates multi-step workflows, and integrates AI translation services. |
| **Smart Contract Layer** | `chaincode/` | Chaincode Contracts, Ledger Verification, State Transition Rules | Decentralized logic enforcing immutable transaction records, multi-party approval state machines, and tamper-proof audit trails. |
| **Persistence & External Services** | Infrastructure | Relational / Document Store, AI Speech/Vision, Azure/AWS Cloud | Maintains operational persistent state (user profiles, message logs, document metadata) alongside external language translation and vision models. |

---

## 🔒 Security & Data Integrity

1. **Immutability:** Transaction states and official actions are recorded via `chaincode/` onto a distributed ledger.
2. **Persistence:** Asynchronous workers ensure that requests are never dropped during transient gateway downtime or long-running administrative processes.
3. **Multi-lingual Accessibility:** Language processing happens at the gateway layer before state submission, normalizing requests across regional dialects.
