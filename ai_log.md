# — Architect's Log & Prompt Strategy

---

## ### 🔴 High-Fidelity Prompt Architecture:

> **Role:** Act as a Principal Systems Architect at a FAANG-level organization.
>
> **Task:** Generate a “Gold Standard” Requirements Specification for a lightweight internal Learning Tracker App (10-user scale) following the official project brief.
>
> **Constraints & Tone:**
> * **Strategic Minimalism:** Avoid over-engineering. Maintain a zero-friction, lightweight system philosophy.
> * **Visual Authority:** Use clear ASCII and Mermaid diagrams for architecture, flows, and wireframes. Avoid dense text blocks.
> * **Tech Pragmatism:** Evaluate only the 3 mandated lightweight stacks (No-Code / MERN / Supabase+React) and deliver a decisive architectural recommendation.
> * **Senior Architect Voice:** Write with clarity, precision, and enterprise-level communication standards.
>
> **Specific Output Sections Required:**
> 1. **Executive Summary:** Purpose, business context, and outcome.
> 2. **Product Vision:** Business value proposition tailored to a 10-person team.
> 3. **Personas:** Detailed Admin and Learner profiles with motivations and frustrations.
> 4. **UI/UX Concept:** High-level text wireframes and user-flow diagrams.
> 5. **Key Features:** Expanded coverage of Progress Tracking, Quizzes, and Note-Taking.
> 6. **Functional Specifications:** Strict `FR-XX` formalism, including smart tracking logic and quiz gating rules.
> 7. **Non-Functional Requirements:** Usability, performance, accessibility, reliability.
> 8. **Tech Stack Verdict:** Comparison across No-Code, MERN, and Supabase+React with a clear final recommendation.
> 9. **Architecture Section:** C4-style component view, system flow diagrams, and lightweight data model explanations.
> 10. **Testing Strategy:** Functional, UX, validation, and accuracy checks.
> 11. **AI Log / Architect Log:** Document prompts used, architectural decisions, and iteration timeline.



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
