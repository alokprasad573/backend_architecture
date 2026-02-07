# System Architecture & Hierarchy Flowcharts

## 1. Hierarchical User Role Structure
This flowchart illustrates the levels of access and control within the Suvidha-Lite+ Dashboard.

```mermaid
graph TD
    %% Nodes
    SA["Super Admin"]
    A["Admin"]
    M["Manager"]
    U["End User"]

    %% Relationships
    SA -->|Creates/Manages| A
    A -->|Creates/Manages| M
    M -->|Onboards/Supports| U

    %% Responsibilities
    subgraph "Super Admin Level (Central)"
        SA_Dashboard["Monitoring & Global Config"]
        SA --> SA_Dashboard
    end

    subgraph "Admin Level (Regional/Zone)"
        A_Dashboard["Region Reporting & Mgmt"]
        A --> A_Dashboard
    end

    subgraph "Manager Level (Local)"
        M_Dashboard["User Operations & Approval"]
        M --> M_Dashboard
    end

    subgraph "User Level"
        U_Dashboard["Service Requests & Status"]
        U --> U_Dashboard
    end

    %% Data Flow
    U_Dashboard -.->|Submit Request| M_Dashboard
    M_Dashboard -.->|Escalate/Report| A_Dashboard
    A_Dashboard -.->|Sync Data| SA_Dashboard
```

## 2. Backend System Architecture
This diagram shows how the Technical Architecture supports the hierarchy.

```mermaid
graph LR
    UserClient["Client Browser - React Admin Dashboard"]
    
    subgraph "Backend Services - Node.js Express"
        AuthMiddleware["Auth Middleware - JWT RBAC"]
        API_Controllers["API Controllers"]
        API_Routes["API Routes"]
    end
    
    DBNode[("MongoDB")]
    
    UserClient -->|"HTTP Request Token"| AuthMiddleware
    AuthMiddleware -->|"Verify Role and Scope"| API_Routes
    API_Routes --> API_Controllers
    API_Controllers -->|"Query Scoped Data"| DBNode
    
    %% Scoping logic
    NoteNode["Role-Based Data Access Layer checks Hierarchy Level before querying DB"]
    AuthMiddleware -.-> NoteNode
```

## 3. Data Flow Diagrams (DFD)

### Level 0: Context Diagram
This diagram represents the interaction between valid Users and the System.

```mermaid
graph LR
    U_Entity["User / Admin / Manager"]
    Sys_App["SUVIDHA-LITE System"]
    
    U_Entity -->|"1. Login Credentials"| Sys_App
    U_Entity -->|"2. Dashboard Requests"| Sys_App
    U_Entity -->|"3. User Mgmt Actions"| Sys_App
    
    Sys_App -->|"4. Auth Token"| U_Entity
    Sys_App -->|"5. Reports & Analytics"| U_Entity
    Sys_App -->|"6. Status Updates"| U_Entity
```

### Level 1: Data Flow Breakdown
This diagram details the flow of data through the internal processes and data stores.

```mermaid
graph TD
    %% External Entity
    UserEntity["User Entity"]
    
    %% Processes
    P1["1.0 Authentication"]
    P2["2.0 Dashboard Controller"]
    P3["3.0 User Management"]
    
    %% Data Stores
    D1[("User DB")]
    D2[("Activity Logs")]
    D3[("Reports/Data Cache")]

    %% Flow 1: Auth
    UserEntity -->|Credentials| P1
    P1 -->|"Validate User"| D1
    D1 -.->|"User Profile"| P1
    P1 -->|"Log Login"| D2
    P1 -->|"JWT Token"| UserEntity

    %% Flow 2: Dashboard
    UserEntity -->|"Request Stats"| P2
    P2 -->|"Query Data"| D1
    P2 -->|"Query Logs"| D2
    D1 -.->|"User Counts"| P2
    D2 -.->|"Activity Stats"| P2
    P2 -->|"Save Cache"| D3
    P2 -->|"Dashboard Data"| UserEntity

    %% Flow 3: User Mgmt
    UserEntity -->|"Create/Update User"| P3
    P3 -->|"Check Permissions"| P1
    P3 -->|"Write User Data"| D1
    P3 -->|"Log Action"| D2
```
