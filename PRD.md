# Product Requirements Document (PRD)
## Project: Lightweight Learning Tracker
**Version:** 2.0  
**Status:** Draft  
**Author:** Aravind Kumar (Enhanced by AI Agent)  
**Date:** 2026-02-08

---

## 1. Executive Summary
The **Lightweight Learning Tracker** is a streamlined, web-based internal tool designed to facilitate continuous learning for small teams. Unlike complex Enterprise Learning Management Systems (LMS), this application focuses on simplicity, speed, and visibility. It allows managers to assign curated learning materials (videos, articles) and enables team members to track their progress, validate knowledge through quizzes, and maintain personal learning notes.

**Core Philosophy:** "Learning functionality without the enterprise bloat."

## 2. Problem Statement
Small teams often struggle with informal learning processes:
- **Fragmentation:** Links to tutorials and docs are buried in Slack/Discord or emails.
- **Lack of Visibility:** Managers don't know who has watched the required training videos.
- **No Validation:** There is no easy way to verify if the content was understood.
- **Context Switching:** Learners have nowhere to take notes alongside the content.

## 3. Goals & Success Metrics
### 3.1 Primary Goals
1.  **Centralization:** Consolidate all learning assignments in one dashboard.
2.  **Accountability:** Provide transparent progress tracking (Not Started, In Progress, Completed).
3.  **Knowledge Retention:** Enforce active learning via mandatory quizzes.
4.  **Simplicity:** Zero-training onboarding for new users.

### 3.2 Success Metrics (KPIs)
-   **Manager Time-to-Assign:** < 2 minutes to create and assign a module.
-   **Learner Engagement:** 100% of assigned critical modules completed within deadline.
-   **System Performance:** Dashboard load time < 500ms.

---

## 4. User Personas

### 4.1 Admin (Team Lead/Manager)
*   **Goal:** Efficiently upskill the team without micromanagement.
*   **Needs:** Quick module creation, high-level progress overview, automated "nudges" (passive visibility of non-completion), and easy quiz result review.
*   **Frustrations:** Spreadsheets for tracking, asking "did you watch that?" repeatedly.

### 4.2 Learner (Team Member)
*   **Goal:** Clear understanding of what needs to be learned and by when.
*   **Needs:** One simple list of tasks, distraction-free learning view, notes integration, and immediate quiz feedback.
*   **Frustrations:** Confusing LMS interfaces, losing track of important links.

---

## 5. Functional Requirements & Modules

The system is divided into **5 Core Modules**.

### Module 1: Authentication & User Management
*   **R1.1 Login/Signup:** Email & Password authentication.
*   **R1.2 Role Management:** 
    *   **Admin:** Full access to create content, view all progress, manage users.
    *   **Learner:** Read-only access to content, write access to own progress/notes/quiz attempts.
*   **R1.3 Profile:** Basic profile (Name, Role, Avatar).
*   **R1.4 Password Reset:** Secure reset flow.

### Module 2: Admin Workspace (Content Management)
*   **R2.1 Module Creation:** 
    *   Title, Description, Estimated Duration.
    *   **Resources:** Embed YouTube/Vimeo links, External URLs, PDF attachments.
    *   **Priority:** High/Medium/Low.
*   **R2.2 Assignments:** Assign modules to "All Users" or specific individuals.
*   **R2.3 Quiz Builder:**
    *   Create multiple-choice questions for each module.
    *   Set passing threshold (e.g., 80%).
    *   Define max attempts (optional).

### Module 3: Learning Experience (Learner View)
*   **R3.1 Dashboard:**
    *   **"Up Next":** Critical/Due modules.
    *   **Progress Board:** Kanban-style or List view status (To Do, In Progress, Done).
*   **R3.2 Module Player:**
    *   Split-screen layout: Content on left, Notes/Context on right.
    *   "Mark as Complete" button (disabled until Quiz passed, if applicable).
*   **R3.3 Personal Knowledge Base (Notes):**
    *   Rich-text editor associated with each module.
    *   Private to the learner.
    *   Auto-saving.

### Module 4: Quiz & Validation Engine
*   **R4.1 Quiz Interface:**
    *   Distraction-free modal or page.
    *   Immediate feedback after submission.
    *   "Try Again" flow for failed attempts.
*   **R4.2 Scoring:** Calculation of percentage score.
*   **R4.3 Locking:** Prevent module completion until quiz is passed.

### Module 5: Analytics & Reporting
*   **R5.1 Team Overview (Admin):**
    *   Heatmap or Progress Bar table showing all learners vs. all modules.
    *   Filter by "Overdue" or "Low Scores".
*   **R5.2 Learner Report (Individual):**
    *   Personal completion rate.
    *   History of completed modules and quiz scores.

---

## 6. Technical Architecture

### 6.1 System Architecture Diagram
The system follows a classic 3-tier architecture, optimized for lightweight deployment.

```mermaid
graph TD
    Client[Client (React SPA)]
    LB[Load Balancer / CDN]
    API[Node.js + Express API]
    DB[(MongoDB Atlas)]
    
    Client -->|HTTPS| LB
    LB --> API
    API -->|Mongoose ODM| DB
    
    subgraph "External Services"
        Auth0[Auth Provider (Optional)]
        S3[Asset Storage (Optional)]
    end
    
    API -.-> Auth0
    API -.-> S3
```

### 6.2 Component Architecture (Frontend)
The frontend is built using **React** with a modular component structure to ensure reusability.

*   **App Shell:** `Layout`, `Navbar`, `Sidebar`
*   **Auth:** `LoginForm`, `ProtectRoute`
*   **Dashboard:** `ProgressWidget`, `ModuleList`, `StatCard`
*   **Learning:** `VideoPlayer`, `PDFViewer`, `NotesEditor`
*   **Quiz:** `QuestionCard`, `Timer`, `ResultModal`

### 6.3 Database Schema Design (MongoDB)

**User Collection**
*   `_id`: ObjectId
*   `email`: String (Unique, Indexed)
*   `password`: String (Bcrypt Hash)
*   `role`: String ('admin' | 'learner')
*   `createdAt`: Date

**Module Collection**
*   `_id`: ObjectId
*   `title`: String
*   `description`: String
*   `content`: Object (Video URL, Text Body, PDF Link)
*   `duration`: Number (minutes)
*   `order`: Number (for curriculum sequencing)
*   `quiz`: Object (Embedded Quiz Schema)

**Progress Collection**
*   `_id`: ObjectId
*   `userId`: ObjectId (Ref: User)
*   `moduleId`: ObjectId (Ref: Module)
*   `status`: String ('locked' | 'active' | 'completed')
*   `quizScore`: Number
*   `notes`: String
*   `lastAccessed`: Date

### 6.4 API Specification (RESTful)

| Method | Endpoint | Access | Description |
| :--- | :--- | :--- | :--- |
| **Auth** | | | |
| POST | `/api/auth/login` | Public | Authenticates user, returns JWT (HttpOnly). |
| POST | `/api/auth/logout` | Public | Clears auth cookie. |
| GET | `/api/auth/me` | Protected | Returns current user profile. |
| **Modules** | | | |
| GET | `/api/modules` | Protected | Lists all accessible modules. |
| POST | `/api/modules` | Admin | Creates a new learning module. |
| GET | `/api/modules/:id` | Protected | Get full module details. |
| **User Progress** | | | |
| GET | `/api/progress` | Admin | Get global team progress stats. |
| POST | `/api/modules/:id/complete` | Learner | Marks module as complete (if quiz passed). |
| POST | `/api/modules/:id/quiz` | Learner | Submits quiz answers for grading. |

### 6.5 Security Architecture
1.  **Authentication:** JWT (JSON Web Tokens) with a short expiry (15 min) + Refresh Token rotation.
2.  **Transport:** TLS 1.2+ forced on all connections.
3.  **Data Validation:** Zod or Joi schemas for all API inputs to prevent injection.
4.  **CORS:** Strict origin policies allowing only the frontend domain.
5.  **Rate Limiting:** `express-rate-limit` to prevent brute force on login.

### 6.6 Deployment Strategy
*   **Frontend:** Vercel (Auto-deploy from Git).
*   **Backend:** Render or Railway (Containerized Node.js).
*   **Database:** MongoDB Atlas (Managed Cluster).
*   **CI/CD:** GitHub Actions for linting and testing before merge.

---

## 7. UI/UX Guidelines
*   **Design System:** Rythu Mitra inspired Minimalist.
*   **Typography:** Inter or Geist Sans. Clean, high legibility.
*   **Color Palette:**
    *   Primary: Indigo/Slate (Professional, calm).
    *   Success: Emerald Green (Completion).
    *   Warning/Action: Amber/Orange.
    *   Background: Off-white/Light Gray structure with White cards.
*   **Responsiveness:** Mobile-first approach for learners (consuming content on the go).

## 8. Non-Functional Requirements
*   **Performance:** API response time < 200ms.
*   **Security:** 
    *   Inputs sanitized (prevent XSS/Injection).
    *   Auth middleware on all protected routes.
*   **Scalability:** Support up to 50 concurrent users (Phase 1), architecture ready for 500+.

## 9. Implementation Roadmap
*   **Phase 1 (MVP - Week 1-2):** Auth, Module CRUD, Assignment, Basic Completion Toggle.
*   **Phase 2 (Validation - Week 3):** Quiz Engine, Scoring, Notes System.
*   **Phase 3 (Polish - Week 4):** Dashboards, Analytics, UI Refinement.

---
**Approval:**
__________________ (Product Manager)  
__________________ (Lead Developer)
