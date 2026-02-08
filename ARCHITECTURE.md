# SUVIDHA-LITE+ Backend & System Architecture

## 1. Overview
This architectural solution is designed for the **C-DAC SUVIDHA Challenge 2026**. The goal is to provide a **Unified Civic Services KIOSK Interface** for Electricity, Gas, and Municipal services. 

The architecture follows a **Modular Microservices-Ready** approach using the **MERN Stack** (MongoDB, Express, React, Node.js), optimized for scalability, security, and real-time citizen interaction.

---

## 2. Technical Stack
*   **Front-end**: React.js (Kiosk Interface & Admin Dashboard)
*   **Back-end**: Node.js & Express.js (Micro-architected Services)
*   **Database**: 
    *   **PostgreSQL**: For transactional data, bill payments, and user credentials (as per Guidelines 3.5).
    *   **MongoDB**: For unstructured activity logs, grievance details, and real-time notifications.
*   **State Management**: Redux Toolkit (for Multilingual UI state)
*   **Authentication**: JWT (JSON Web Tokens) with AES-256 encryption.
*   **Security**: TLS/SSL for communication, DPDP Act compliance for PII (Personally Identifiable Information).

---

## 3. Core Modules & Microservices
To ensure a "loosely coupled" architecture (Guideline 3.5), the system is divided into functional services:

### A. Auth & Identity Service (`auth-service`)
*   Multi-factor authentication (OTP/PIN/Biometric simulation).
*   Hierarchical RBAC (Super Admin, Admin, Manager, Citizen).
*   Session management for Kiosk safety (Auto-timeout).

### B. Utility Service (`utility-service`)
*   **Electricity**: Bill fetching, payment history, meter reading submission.
*   **Gas**: New connection requests, status tracking.
*   **Water/Waste**: Municipal fee payments, grievance reporting.

### C. Grievance & Notification Service (`grievance-service`)
*   Ticket lifecycle management.
*   Real-time status updates via WebSockets/Push.
*   Multimedia attachment support (for document uploads).

### D. Admin & Analytics Service (`admin-service`)
*   Kiosk usage monitoring.
*   Regional reporting for Admins and Managers.
*   Content Management (Managing multilingual prompts).

---

## 4. Proposed Directory Structure (Modular Monolith)
For the hackathon development phase, we use a modular folder structure that can be easily split into separate repositories for microservices.

```bash
suvidha-platform/
├── kiosk-ui/                # React Portal for Touch-Kiosk
├── admin-dashboard/         # React Portal for Management
├── backend/
│   ├── src/
│   │   ├── services/
│   │   │   ├── auth/        # Auth & Role Management
│   │   │   ├── electricity/ # Electricity Utility Business Logic
│   │   │   ├── gas/         # Gas Utility Business Logic
│   │   │   ├── municipal/   # Water & Waste Management
│   │   │   └── notification/# Real-time Alerts & Logs
│   │   ├── middleware/      # RBAC, Global Error Handling, DPDP Compliance
│   │   ├── models/          # SQL/NoSQL Schemas
│   │   └── server.js        # Entry point with Router delegation
│   ├── config/              # Database & SSL Config
│   └── .env                 # Secrets & API Keys
└── scripts/                 # Deployment & DB Migration
```

---

## 5. Security & Compliance
*   **DPDP Compliance**: All citizen data is masked in logs. Encrypted storage for sensitive fields (Aadhar/Voter ID hash).
*   **Audit Logging**: Every action on the Kiosk is logged with a Kiosk-ID and Timestamp for forensic tracking.
*   **Input Validation**: Strict Joi/Zod schemas to prevent injection attacks on public touchpoints.

---

## 6. Real-Time Interaction Strategy
*   **Multilingual Support**: Internationalization (i18next) with dynamic JSON loading for Hindi, English, and Regional languages.
*   **WebSocket Integration**: For live "Outage Advisories" and "Queue Status" displayed on the Kiosk idle screen.
