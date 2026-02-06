# SkillVector: Enterprise Competency Engine — Technical Design Document (TDD)

**Version:** 5.0 (FAANG Architecture Release)  
**Date:** October 26, 2023  
**Status:** **APPROVED FOR ENGINEERING**  
**Classification:** Confidential / Internal Engineering  
**Repository:** `skill-vector-core`

---

## 1. Executive Directive

**SkillVector** is a high-velocity competence acquisition engine designed to optimize the "Mean Time to Proficiency" (MTTP) for engineering squads. Unlike passive LMS (Learning Management Systems), SkillVector employs an **Event-Driven Architecture** to enforce active engagement, cryptographic-grade verification, and friction-free knowledge ingestion.

**Strategic North Star:**
Achieve a **5x reduction in administrative overhead** while guaranteeing **100% compliance auditability** through automated, heartbeat-driven telemetry.

---

## 2. User Matrix & Psychological Profiles

### 2.1 Admin Persona: "The Engineering Director" (Mike)
*   **Role:** Technical Leadership / Governance.
*   **Cognitive Model:** "Trust but Verify."
*   **SLO Requirement:** **< 200ms** Time-to-Insight. He requires an aggregated "Traffic Light" telemetry board to assess team health instantly.
*   **Anti-Pattern to Avoid:** The "Nags Dashboard" (Manually chasing direct reports).

### 2.2 Learner Persona: "The 10x Engineer" (Sarah)
*   **Role:** Senior Software Engineer.
*   **Cognitive Model:** "Flow State Preservation."
*   **Requirement:** Context-Attached Memory. She demands that notes be deeply linked to specific video frames (Time-Index Binding) to reduce cognitive load during recall.
*   **Anti-Pattern to Avoid:** Context Switching. The UI must be a "Single Pane of Glass."

---

## 3. Architectural Stack & Decision Records (ADR)

*We evaluated three architectures against the **"Velocity vs. Robustness"** quadrant. The objective was to minimize OpEx (Operational Expenditure) while maximizing extensibility.*

### Option 1: No-Code/Low-Code (Airtable + Softr)
*   **Architectural Verdict:** **REJECTED.**
*   **Reasoning:** Fails to support **Custom Event Emitters** (required for the video heartbeat logic). Tightly coupled UI prevents the "Deep Linking" requirement (FR-07).

### Option 2: Monolithic MERN (Mongo, Express, React, Node)
*   **Architectural Verdict:** **REJECTED.**
*   **Reasoning:** Introduces unnecessary **DevOps Complexity**. Managing a stateful Node.js cluster for <100 concurrent connections is a violation of the "YAGNI" (You Aren't Gonna Need It) principle.

### Option 3: Distributed Serverless (Supabase + React on Edge) — **SELECTED**
*   **Architectural Verdict:** **APPROVED.**
*   **Core Advantages:**
    *   **Data Consistency:** Postgres + RLS ensures Strong Consistency (ACID compliance) for compliance records.
    *   **Latency:** Edge-cached React Client ensures **O(1)** access to module content.
    *   **Security:** "Zero Trust" model via Row-Level Security (RLS) handled at the database layer.

---

## 4. UI/UX Blueprint & Wireframes

**Design System:** "Neumorphic Dark" — High contrast, minimal noise.

### 4.1 The Command Center (Admin View)
*   **Visual Metaphor:** Mission Control Status Board.

```text
+-------------------------------------------------------------+
|  [SkillVector]  ::  ENGINEERING COMPETENCY MATRIX           |
+-------------------------------------------------------------+
|                                                             |
|  🟢 SYSTEM HEALTH: 92% Compliance | ⚠️ 1 Squad Risk         |
|                                                             |
|  USER_ID          MODULE_HASH            TELEMETRY   SCORE  |
|  ---------------  ---------------------  ----------  -----  |
|  dev.sarah        sha256:react_hooks     ✅ SYNCED    100%  |
|  dev.john         sha256:pg_index        ⏳ PENDING    --   |
|  dev.mike         sha256:git_flow        🔴 LATENCY    --   |
|                                                             |
|  [> INITIATE_NUDGE_PROTOCOL (1) ]                           |
+-------------------------------------------------------------+
```

### 4.2 The Focus Interface (Learner View)
*   **Visual Metaphor:** IDE (Integrated Development Environment).

```text
+---------------------------------------------------------------+
|  [SkillVector]  ::  MODULE_EXECUTION_ENV           [Avatar]   |
+---------------------------------------------------------------+
|                                                               |
|   🎯 CONTEXT: System Design / CAP Theorem                     |
|                                                               |
|   [ VIDEO PLAYER ----------------------------------------- ]  |
|   [                                                        ]  |
|   [   ( Playing: 14:02 / 22:00 )                           ]  |
|   [                                                        ]  |
|   [ ------------------------------------------------------ ]  |
|                                                               |
|   📝 MEMORY BUFFER (Notes)                                    |
|   > 12:05: Partition Tolerance is non-negotiable in dist...   |
|   > 14:00: [User Typing...]                                   |
|                                                               |
+---------------------------------------------------------------+
```

---

## 5. Functional Specifications (FRD)

### 5.1 Telemetry & Progress
*   **FR-CORE-01 (Heartbeat Protocol):** Client emits a signed `heartbeat` event every 30s. Server validates timestamp delta to prevent "Fast-Forward" spoofing.
*   **FR-CORE-02 (Gatekeeper Logic):** Completion State is strictly binary.
    `isComplete = (videoProgress > 0.9) && (quizScore > 0.8)`

### 5.2 Contextual Memory (Notes)
*   **FR-CORE-03 (Temporal Binding):** Notes are stored as a tuple `(userId, moduleId, timestamp, content)`.
*   **FR-CORE-04 (Deep Seek):** Invoking a note triggers a `player.seek(timestamp)` event, restoring context instantly (Time-to-Interactive < 100ms).

### 5.3 Knowledge Verification
*   **FR-CORE-05 (Lockdown Mode):** Usage of `React.Context` to disable Quiz UI routes until `VIDEO_COMPLETE` event is fired.

---

## 6. Non-Functional Requirements (SLOs)

*   **P99 Latency:** Dashboard load time **< 200ms**.
*   **Availability:** 99.9% (Serverless Fallback).
*   **Security:**
    *   **Transit:** TLS 1.3 (ChaCha20-Poly1305).
    *   **At Rest:** AES-256 GCM.
    *   **Authorization:** JWT (RS256) with strict Row-Level Security.

---

## 7. System Architecture (C4 Model)

```mermaid
graph TD
    User((Engineer))
    edge[Edge CDN]
    spa[Single Page App]
    
    subgraph "Infrastructure Layer (Supabase)"
        auth[GoTrue Auth Service]
        db[(PostgreSQL Cluster)]
        api[Auto-Generated REST API]
    end

    User -->|HTTPS/2| edge
    edge -->|Serve Assets| spa
    spa -->|Identity (JWT)| auth
    spa -->|Query (JSON)| api
    api -->|SQL Transactions| db
```

---

## 8. Deployment pipeline (CI/CD)

*   **Repository:** `git@github.com:company/skill-vector-core.git`
*   **Pipeline Strategy:**
    1.  **Commit:** Developer pushes to `feature/*`.
    2.  **Verify:** GitHub Action runs `vitest` suite.
    3.  **Merge:** PR merged to `main`.
    4.  **Deploy:**
        *   **Frontend:** Immutable deployment to Vercel.
        *   **Backend:** Schematic migration applied via `supabase db push`.

---
