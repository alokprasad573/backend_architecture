# SUVIDHA System Flowcharts & Interaction Models

## 1. Unified Kiosk User Experience (CX) Flow
This flowchart describes the citizen's journey from language selection to service completion.

```mermaid
graph TD
    Start([Kiosk Idle Screen]) --> Lang[Select Language - Hindi/English/Regional]
    Lang --> Auth{Citizen Login/Auth}
    
    Auth -- Direct Access --> Guest[View Alerts/Emergency Info]
    Auth -- Secure Login --> Dashboard[Unified Service Dashboard]
    
    Dashboard --> ServiceA[Electricity: Pay Bill / New Meter]
    Dashboard --> ServiceB[Gas: Connection Status / Request]
    Dashboard --> ServiceC[Municipal: Water / Waste / Grievance]
    
    ServiceA --> Pay[Secure Payment Gateway]
    ServiceB --> Upload[Document Upload / Scan]
    ServiceC --> Ticket[Grievance Submission]
    
    Pay --> Print[Receipt Generation & Local Cache]
    Upload --> Status[Live Tracking ID]
    Ticket --> Notify[Real-time Admin Alert]
    
    Print --> End([Logout & Data Wipe])
    Status --> End
    Notify --> End
```

---

## 2. Microservices Backend Architecture
Detailed view of how loosely coupled services communicate via a Shared API Gateway.

```mermaid
graph LR
    subgraph "Kiosk / Client Layer"
        TouchUI["Touch Interface (React)"]
        Scanner["Doc Scanner/Printer API"]
    end

    subgraph "API Gateway"
        Gateway["Express Gateway / Load Balancer"]
        RBAC["Role & DPDP Middleware"]
    end

    subgraph "Service Layer (Microservices)"
        AuthSvc["Auth Service"]
        UtilSvc["Utility Service (Elec/Gas)"]
        MunSvc["Municipal Service"]
        NotifSvc["Grievance & Alerts"]
    end

    subgraph "Persistence Layer"
        DB_SQL[("PostgreSQL: Transactions")]
        DB_NoSQL[("MongoDB: Logs/Alerts")]
    end

    TouchUI <--> Gateway
    Gateway --> RBAC
    RBAC --> AuthSvc
    RBAC --> UtilSvc
    RBAC --> MunSvc
    RBAC --> NotifSvc

    AuthSvc & UtilSvc --> DB_SQL
    MunSvc & NotifSvc --> DB_NoSQL
```

---

## 3. Administrative & Monitoring Hierarchy
Flow of data from local Kiosks to Regional and Central Admins.

```mermaid
graph TD
    K1[Kiosk 001] --> M1[Local Manager Dashboard]
    K2[Kiosk 002] --> M1
    
    M1 -->|Escalate/Aggregated Reports| A1[Regional Admin - West Zone]
    M2[Other Managers] --> A1
    
    A1 -->|Strategic Data Sync| SA[Super Admin - Central C-DAC]
    
    subgraph "Control Capabilities"
        SA -->|Global Policy Update| A1
        A1 -->|Local Service Config| M1
        M1 -->|Immediate Kiosk Alert| K1
    end
```

---

## 4. Grievance Redressal Lifecycle
How service requests are handled across the hierarchy.

```mermaid
sequenceDiagram
    participant C as Citizen (Kiosk)
    participant M as Manager (Local)
    participant A as Admin (Regional)
    participant DB as System DB

    C->>+DB: Submit Grievance + Document
    DB-->>M: Notify New Ticket
    M->>+M: Review & Verify Document
    alt Valid Request
        M->>DB: Update Status: 'In Progress'
        DB-->>C: Real-time Notification (Kiosk/SMS)
    else Complex Issue
        M->>A: Escalate to Regional Level
        A->>DB: Approve/Resolve
    end
    DB-->>C: 'Request Completed' + Print Receipt
```


## 5. System Security & Compliance
Detailed view of security measures and compliance protocols.

```mermaid
graph TB
    subgraph "I. Citizen Interaction Layer"
        direction LR
        Kiosk["Touch Kiosk UI (React/Angular)"]
        CitizenWA["Citizen WhatsApp Mobile"]
    end

    subgraph "II. Security & Edge Layer"
        AGW["Kong API Gateway"]
        Auth["Auth Service (OAuth2/JWT)"]
    end

    subgraph "III. Core Business Logic (Microservices)"
        direction TB
        Utility["Utility Microservice (Elec/Gas/Mun)"]
        Payment["Payment Service"]
        Grievance["Grievance/Status Service"]
    end

    subgraph "IV. Integration & Notifications"
        NOTIF["Notification Engine"]
        WAGW["WhatsApp API Bridge"]
        Webhook["Webhook Listener"]
    end

    subgraph "V. Persistence Layer"
        DB[(PostgreSQL: Core Data)]
        Logs[(Audit Logs: ELK Stack)]
    end

    %% Interaction Flows
    Kiosk -->|HTTPS/REST| AGW
    AGW --> Auth
    Auth -.->|Validates| AGW
    
    %% Service Routing
    AGW --> Utility
    AGW --> Payment
    AGW --> Grievance

    %% Data Connections
    Utility & Payment & Grievance --> DB
    Utility & Payment & Grievance --> Logs

    %% WhatsApp Outbound Notification
    Payment & Grievance -->|Success Event| NOTIF
    NOTIF -->|Push PDF/Link| WAGW
    WAGW -->|Message| CitizenWA

    %% WhatsApp Inbound Query
    CitizenWA -->|'Query Status'| Webhook
    Webhook -->|Parse Request| Grievance
    Grievance -->|Fetch| DB
    Grievance -->|Response| NOTIF

    %% Styling
    style Kiosk fill:#f9f,stroke:#333
    style CitizenWA fill:#25D366,color:#fff
    style AGW fill:#69f,stroke:#333
    style NOTIF fill:#ff9,stroke:#333

```