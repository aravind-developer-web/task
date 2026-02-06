# Deliverable B — Architect's Log & Prompt Strategy

**Subject:** Automated Requirements Generation for Learning Tracker App  
**Role:** Principal Systems Architect  
**Date:** October 26, 2023  
**Classification:** Development Artifact

---

## 1. Proprietary Meta-Prompt (The Input)
*To generate the Senior-Level Requirements Document (V4.2), the following engineered prompt was utilized.*

### 🔴 High-Fidelity Prompt Architecture:
> **Role:** Act as a Principal Systems Architect at a FAANG company.
> 
> **Task:** Create a "Gold Standard" Requirements Specification for a lightweight internal Learning Tracker App (10-user scale).
> 
> **Constraints & Tone:**
> *   **Strategic Minimalism:** Do not over-engineer. The solution must be "Zero-Friction".
> *   **Visual Authority:** Use ASCII/Mermaid diagrams for flows. No wall-of-text.
> *   **Tech Pragmatism:** Evaluate 3 specific stacks (No-Code vs MERN vs SupaStack) and provide a hard recommendation.
> 
> **Specific Output Sections:**
> 1.  **Product Vision:** Business Value proposition.
> 2.  **Personas:** Detailed profiles of Admin and Learner.
> 3.  **UI/UX Concept:** High-fidelity text wireframes and Flow Diagrams.
> 4.  **Functional Specs:** Strict `FR-XX` format with "Smart Tracking" logic.
> 5.  **Tech Stack Verdict:** A comparative analysis ending in a clear winner.

---

## 2. AI Response & Architectural Decisions (The Output)

### ADR-01: Technology Stack Selection
*   **Context:** The client requires a "lightweight" solution for 10 users.
*   **Decision:** Selected **Option 3 (Supabase + React)**.
*   **Reasoning:**
    *   *MERN* requires maintaining a robust backend server, which is unnecessary OpEx.
    *   *Supabase* provides the "Heavy Lifting" (Auth, DB, Realtime) as a managed service.
    *   **Result:** 80% reduction in backend boilerplate code.

### ADR-02: UX Interaction Model ("The Deep Link")
*   **Context:** Learners struggle to recall context from videos watched days ago.
*   **Decision:** Implementation of **Time-Index Linking (FR-07)**.
*   **Mechanism:** `onClick(Note) => Player.seekTo(Note.timestamp)`.
*   **Impact:** Transforms the app from a passive "Viewer" to an active "Knowledge Base".

### ADR-03: Anti-Cheat Logic vs. UX
*   **Context:** Management requires compliance, but learners hate friction.
*   **Decision:** **Passive Heartbeat Tracking (FR-01)**.
*   **Mechanism:** Checks for `isPlaying` states every 30 seconds.
*   **Result:** "Invisible Compliance". The learner watches naturally, and the system verifies in the background.

---

## 3. Iteration History

| Version | Focus | Key Changes |
| :--- | :--- | :--- |
| **v2.0** | Refinement | Added "Anti-Cheat" logic and ASCII Wireframes. |
| **v3.0** | Enterprise | Added Mermaid Diagrams and namespaced IDs (`FR-CORE`). |
| **v3.5** | Scope | Added Admin Dashboard wireframes. |
| **v4.0** | Architecture | Added C4 Component Diagram and CI/CD Pipeline. |
| **v4.2** | **STABLE** | **Project Restoration.** Finalized layout for delivery. |

---
