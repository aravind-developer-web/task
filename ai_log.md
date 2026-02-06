# Deliverable B — Architect's Log & Prompt Strategy

**Subject:** Automated Requirements Generation for Learning Tracker App  
**Role:** Principal Systems Architect  
**Date:** October 26, 2023  
**Classification:** Development Artifact

---

## 1. Proprietary Meta-Prompt (The Input)
*To generate the Senior-Level Requirements Document (V3.2), the following engineered prompt was utilized to condition the AI model into a strict "Architect Mode".*

### 🔴 High-Fidelity Prompt Architecture:
> **Role:** Act as a Principal Systems Architect at a FAANG company.
> 
> **Task:** Create a "Gold Standard" Requirements Specification for a lightweight internal Learning Tracker App (10-user scale).
> 
> **Constraints & Tone:**
> *   **Strategic Minimalism:** Do not over-engineer. The solution must be "Zero-Friction".
> *   **Visual Authority:** Use ASCII/Mermaid diagrams for flows. No wall-of-text.
> *   **Tech Pragmatism:** Evaluate 3 specific stacks (No-Code vs MERN vs SupaStack) and provide a hard recommendation based on "Velocity vs. Robustness".
> 
> **Specific Output Sections:**
> 1.  **Product Vision:** Business Value proposition.
> 2.  **Personas:** Detailed profiles of Admin and Learner.
> 3.  **UI/UX Concept:** High-fidelity text wireframes and Flow Diagrams.
> 4.  **Functional Specs:** Strict `FR-XX` format with "Smart Tracking" logic.
> 5.  **Tech Stack Verdict:** A comparative analysis ending in a clear winner.
> 
> **Input Command:** "Generate requirements_doc.md adhering strictly to the above constraints."

---

## 2. AI Response & Architectural Decisions (The Output)
*Below is a summary of the AI's reasoning process and the resulting Architectural Decision Records (ADR) incorporated into the final document.*

### ADR-01: Technology Stack Selection
*   **Context:** The client requires a "lightweight" solution for 10 users but rejected "No-Code" due to UX rigidity.
*   **Decision:** Selected **Option 3 (Supabase + React)**.
*   **Reasoning:**
    *   *MERN* requires maintaining a robust backend server (Express/Node), which is unnecessary OpEx (Operational Expenditure) for 10 users.
    *   *Supabase* provides the "Heavy Lifting" (Auth, DB, Realtime) as a managed service, allowing development to focus purely on the *differentiator* (The Video+Note UX).
    *   **Result:** 80% reduction in backend boilerplate code.

### ADR-02: UX Interaction Model ("The Deep Link")
*   **Context:** Learners struggle to recall context from videos watched days ago.
*   **Decision:** Implementation of **Time-Index Linking (FR-07)**.
*   **Mechanism:** `onClick(Note) => Player.seekTo(Note.timestamp)`.
*   **Impact:** Transforms the app from a passive "Viewer" to an active "Knowledge Base".

### ADR-03: Anti-Cheat Logic vs. UX
*   **Context:** Management requires compliance, but learners hate friction.
*   **Decision:** **Passive Heartbeat Tracking (FR-01)**.
*   **Mechanism:** Instead of "Quizzes every 5 minutes" (High Friction), we check for `isPlaying && !isSeeking` states every 30 seconds.
*   **Result:** "Invisible Compliance". The learner watches naturally, and the system verifies in the background.

---

## 3. Iteration History

| Version | Focus | Key Changes |
| :--- | :--- | :--- |
| **v1.0** | Draft | Initial functional list. Rejected for lack of depth. |
| **v2.0** | Refinement | Added "Anti-Cheat" logic and ASCII Wireframes. |
| **v3.0** | Enterprise | Added Mermaid Diagrams and namespaced IDs (`FR-CORE`). |
| **v3.5** | Scope | Added Admin Dashboard wireframes. |
| **v4.0** | **ADVANCED** | **Architecture & Deployment.** Added C4 Component Diagram, State Machine Logic, and CI/CD Pipeline strategy. |

---
