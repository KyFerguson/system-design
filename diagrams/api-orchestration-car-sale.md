# API orchestration: enterprise vehicle sale

```mermaid
sequenceDiagram
    autonumber
    actor Dealer as Dealer Portal
    participant Orchestrator as Vehicle Sale Orchestrator
    participant CRM as Customer / CRM
    participant Inventory as Inventory Service
    participant Finance as Credit & Finance
    participant Payment as Payment Service
    participant Title as Title & Registration
    participant Contract as Contract Service
    participant Notify as Notification Service

    Dealer->>Orchestrator: POST /vehicle-sales
    activate Orchestrator
    Orchestrator->>CRM: Validate customer and compliance
    CRM-->>Orchestrator: Customer approved
    Orchestrator->>Inventory: Reserve vehicle and calculate price
    Inventory-->>Orchestrator: Vehicle reserved, price confirmed

    par Run finance check
        Orchestrator->>Finance: Request loan quote
        Finance-->>Orchestrator: Approved terms
    and Run payment check
        Orchestrator->>Payment: Pre-authorize deposit
        Payment-->>Orchestrator: Deposit authorized
    end

    Orchestrator->>Title: Verify title and registration data
    Title-->>Orchestrator: Transfer eligible
    Orchestrator->>Contract: Create purchase contract
    Contract-->>Orchestrator: Contract ID
    Orchestrator->>Inventory: Confirm vehicle allocation
    Inventory-->>Orchestrator: Allocation confirmed
    Orchestrator-)Notify: Publish sale-confirmed event
    Orchestrator-->>Dealer: 201 Created: contract and delivery status
    deactivate Orchestrator

    Note over Orchestrator,Notify: The portal makes one request. The orchestrator owns sequencing, response composition, idempotency, timeout budgets, and tracing.
```