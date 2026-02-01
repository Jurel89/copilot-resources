# DrawIO Diagram Examples

This document provides ready-to-use diagram templates and examples for common use cases.

## Basic Flowchart

### Mermaid Version

```mermaid
graph TD
    Start([Start]) --> Input[/Input Data/]
    Input --> Process[Process Data]
    Process --> Decision{Valid?}
    Decision -->|Yes| Output[/Output Result/]
    Decision -->|No| Error[Handle Error]
    Error --> Process
    Output --> End([End])
```

### DrawIO XML Version

```xml
<?xml version="1.0" encoding="UTF-8"?>
<mxfile host="app.diagrams.net">
  <diagram id="flowchart" name="Basic Flowchart">
    <mxGraphModel dx="0" dy="0" grid="1" gridSize="10">
      <root>
        <mxCell id="0"/>
        <mxCell id="1" parent="0"/>
        <mxCell id="start" value="Start" style="ellipse;whiteSpace=wrap;html=1;fillColor=#d5e8d4;strokeColor=#82b366;" vertex="1" parent="1">
          <mxGeometry x="160" y="40" width="80" height="40" as="geometry"/>
        </mxCell>
        <mxCell id="input" value="Input Data" style="shape=parallelogram;perimeter=parallelogramPerimeter;whiteSpace=wrap;html=1;fillColor=#dae8fc;strokeColor=#6c8ebf;" vertex="1" parent="1">
          <mxGeometry x="140" y="120" width="120" height="50" as="geometry"/>
        </mxCell>
        <mxCell id="process" value="Process Data" style="rounded=1;whiteSpace=wrap;html=1;fillColor=#dae8fc;strokeColor=#6c8ebf;" vertex="1" parent="1">
          <mxGeometry x="140" y="210" width="120" height="50" as="geometry"/>
        </mxCell>
        <mxCell id="decision" value="Valid?" style="rhombus;whiteSpace=wrap;html=1;fillColor=#fff2cc;strokeColor=#d6b656;" vertex="1" parent="1">
          <mxGeometry x="150" y="300" width="100" height="60" as="geometry"/>
        </mxCell>
        <mxCell id="output" value="Output Result" style="shape=parallelogram;perimeter=parallelogramPerimeter;whiteSpace=wrap;html=1;fillColor=#d5e8d4;strokeColor=#82b366;" vertex="1" parent="1">
          <mxGeometry x="40" y="400" width="120" height="50" as="geometry"/>
        </mxCell>
        <mxCell id="error" value="Handle Error" style="rounded=1;whiteSpace=wrap;html=1;fillColor=#f8cecc;strokeColor=#b85450;" vertex="1" parent="1">
          <mxGeometry x="240" y="400" width="120" height="50" as="geometry"/>
        </mxCell>
        <mxCell id="end" value="End" style="ellipse;whiteSpace=wrap;html=1;fillColor=#d5e8d4;strokeColor=#82b366;" vertex="1" parent="1">
          <mxGeometry x="60" y="500" width="80" height="40" as="geometry"/>
        </mxCell>
        <mxCell id="e1" style="edgeStyle=orthogonalEdgeStyle;" edge="1" parent="1" source="start" target="input"><mxGeometry relative="1" as="geometry"/></mxCell>
        <mxCell id="e2" style="edgeStyle=orthogonalEdgeStyle;" edge="1" parent="1" source="input" target="process"><mxGeometry relative="1" as="geometry"/></mxCell>
        <mxCell id="e3" style="edgeStyle=orthogonalEdgeStyle;" edge="1" parent="1" source="process" target="decision"><mxGeometry relative="1" as="geometry"/></mxCell>
        <mxCell id="e4" value="Yes" style="edgeStyle=orthogonalEdgeStyle;" edge="1" parent="1" source="decision" target="output"><mxGeometry relative="1" as="geometry"/></mxCell>
        <mxCell id="e5" value="No" style="edgeStyle=orthogonalEdgeStyle;" edge="1" parent="1" source="decision" target="error"><mxGeometry relative="1" as="geometry"/></mxCell>
        <mxCell id="e6" style="edgeStyle=orthogonalEdgeStyle;" edge="1" parent="1" source="error" target="process"><mxGeometry relative="1" as="geometry"/></mxCell>
        <mxCell id="e7" style="edgeStyle=orthogonalEdgeStyle;" edge="1" parent="1" source="output" target="end"><mxGeometry relative="1" as="geometry"/></mxCell>
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
```

## Web Application Architecture

### Mermaid Version

```mermaid
graph TB
    subgraph Client
        Browser[Web Browser]
        Mobile[Mobile App]
    end
    
    subgraph CDN
        CF[CloudFront/CDN]
    end
    
    subgraph LoadBalancing
        ALB[Application Load Balancer]
    end
    
    subgraph WebTier
        Web1[Web Server 1]
        Web2[Web Server 2]
    end
    
    subgraph AppTier
        App1[App Server 1]
        App2[App Server 2]
    end
    
    subgraph DataTier
        DB[(Primary DB)]
        Cache[(Redis Cache)]
    end
    
    Browser --> CF
    Mobile --> CF
    CF --> ALB
    ALB --> Web1
    ALB --> Web2
    Web1 --> App1
    Web1 --> App2
    Web2 --> App1
    Web2 --> App2
    App1 --> DB
    App1 --> Cache
    App2 --> DB
    App2 --> Cache
```

## Microservices Architecture

### Mermaid Version

```mermaid
graph TB
    subgraph Clients
        Web[Web App]
        iOS[iOS App]
        Android[Android App]
    end
    
    subgraph Gateway
        APIG[API Gateway]
        Auth[Auth Service]
    end
    
    subgraph Services
        UserSvc[User Service]
        OrderSvc[Order Service]
        ProductSvc[Product Service]
        PaymentSvc[Payment Service]
        NotifSvc[Notification Service]
    end
    
    subgraph Messaging
        Kafka[Apache Kafka]
    end
    
    subgraph Databases
        UserDB[(User DB)]
        OrderDB[(Order DB)]
        ProductDB[(Product DB)]
        PaymentDB[(Payment DB)]
    end
    
    Web --> APIG
    iOS --> APIG
    Android --> APIG
    
    APIG --> Auth
    APIG --> UserSvc
    APIG --> OrderSvc
    APIG --> ProductSvc
    APIG --> PaymentSvc
    
    UserSvc --> UserDB
    OrderSvc --> OrderDB
    ProductSvc --> ProductDB
    PaymentSvc --> PaymentDB
    
    OrderSvc --> Kafka
    PaymentSvc --> Kafka
    Kafka --> NotifSvc
```

## Sequence Diagram - User Authentication

### Mermaid Version

```mermaid
sequenceDiagram
    actor User
    participant UI as Web UI
    participant API as API Gateway
    participant Auth as Auth Service
    participant DB as User Database
    participant Token as Token Service
    
    User->>UI: Enter credentials
    UI->>API: POST /auth/login
    API->>Auth: Validate credentials
    Auth->>DB: Query user
    DB-->>Auth: User data
    
    alt Valid credentials
        Auth->>Token: Generate tokens
        Token-->>Auth: Access + Refresh tokens
        Auth-->>API: Success + tokens
        API-->>UI: 200 OK + tokens
        UI-->>User: Redirect to dashboard
    else Invalid credentials
        Auth-->>API: Authentication failed
        API-->>UI: 401 Unauthorized
        UI-->>User: Show error
    end
```

## Class Diagram - E-Commerce Domain

### Mermaid Version

```mermaid
classDiagram
    class User {
        +String id
        +String email
        +String name
        +Address[] addresses
        +register()
        +login()
        +updateProfile()
    }
    
    class Product {
        +String id
        +String name
        +String description
        +Money price
        +int stockQuantity
        +updateStock()
        +getPrice()
    }
    
    class Order {
        +String id
        +User customer
        +OrderItem[] items
        +OrderStatus status
        +Money total
        +calculateTotal()
        +submit()
        +cancel()
    }
    
    class OrderItem {
        +Product product
        +int quantity
        +Money unitPrice
        +getSubtotal()
    }
    
    class Payment {
        +String id
        +Order order
        +Money amount
        +PaymentMethod method
        +PaymentStatus status
        +process()
        +refund()
    }
    
    User "1" --> "*" Order : places
    Order "1" --> "*" OrderItem : contains
    OrderItem "*" --> "1" Product : references
    Order "1" --> "0..1" Payment : has
```

## State Diagram - Order Lifecycle

### Mermaid Version

```mermaid
stateDiagram-v2
    [*] --> Draft
    
    Draft --> Pending : Submit
    Draft --> Cancelled : Cancel
    
    Pending --> Confirmed : Confirm
    Pending --> Cancelled : Cancel
    Pending --> Draft : Edit
    
    Confirmed --> Processing : Start Processing
    Confirmed --> Cancelled : Cancel
    
    Processing --> Shipped : Ship
    Processing --> Cancelled : Cancel
    
    Shipped --> Delivered : Deliver
    Shipped --> Returned : Return
    
    Delivered --> Completed : Complete
    Delivered --> Returned : Return
    
    Returned --> Refunded : Refund
    
    Completed --> [*]
    Refunded --> [*]
    Cancelled --> [*]
```

## Entity Relationship Diagram

### Mermaid Version

```mermaid
erDiagram
    USER ||--o{ ORDER : places
    USER ||--o{ ADDRESS : has
    USER ||--o{ REVIEW : writes
    
    ORDER ||--|{ ORDER_ITEM : contains
    ORDER ||--|| PAYMENT : has
    ORDER ||--o| SHIPPING : has
    
    PRODUCT ||--o{ ORDER_ITEM : "ordered in"
    PRODUCT ||--o{ REVIEW : receives
    PRODUCT }|--|| CATEGORY : "belongs to"
    PRODUCT }|--|| BRAND : "made by"
    
    USER {
        uuid id PK
        string email UK
        string password_hash
        string name
        timestamp created_at
    }
    
    ORDER {
        uuid id PK
        uuid user_id FK
        decimal total
        string status
        timestamp created_at
    }
    
    PRODUCT {
        uuid id PK
        string name
        text description
        decimal price
        int stock
        uuid category_id FK
        uuid brand_id FK
    }
    
    ORDER_ITEM {
        uuid id PK
        uuid order_id FK
        uuid product_id FK
        int quantity
        decimal unit_price
    }
```

## C4 Context Diagram

### Mermaid Version

```mermaid
C4Context
    title System Context Diagram - E-Commerce Platform
    
    Person(customer, "Customer", "A person who wants to buy products online")
    Person(admin, "Admin", "Platform administrator who manages products and orders")
    
    System(ecommerce, "E-Commerce Platform", "Allows customers to browse and purchase products")
    
    System_Ext(payment, "Payment Gateway", "Processes credit card and other payments")
    System_Ext(shipping, "Shipping Provider", "Handles package delivery")
    System_Ext(email, "Email Service", "Sends transactional emails")
    System_Ext(analytics, "Analytics Platform", "Tracks user behavior")
    
    Rel(customer, ecommerce, "Browses products, places orders")
    Rel(admin, ecommerce, "Manages products, processes orders")
    Rel(ecommerce, payment, "Processes payments via")
    Rel(ecommerce, shipping, "Ships orders via")
    Rel(ecommerce, email, "Sends emails via")
    Rel(ecommerce, analytics, "Sends events to")
```

## Network Diagram

### Mermaid Version

```mermaid
graph TB
    subgraph Internet
        Users((Users))
    end
    
    subgraph DMZ
        FW1[Firewall]
        LB[Load Balancer]
    end
    
    subgraph WebZone
        Web1[Web Server 1]
        Web2[Web Server 2]
    end
    
    subgraph AppZone
        App1[App Server 1]
        App2[App Server 2]
    end
    
    subgraph DataZone
        FW2[Internal Firewall]
        DB1[(Primary DB)]
        DB2[(Replica DB)]
    end
    
    Users --> FW1
    FW1 --> LB
    LB --> Web1
    LB --> Web2
    Web1 --> App1
    Web1 --> App2
    Web2 --> App1
    Web2 --> App2
    App1 --> FW2
    App2 --> FW2
    FW2 --> DB1
    DB1 --> DB2
```

## CI/CD Pipeline

### Mermaid Version

```mermaid
graph LR
    subgraph Development
        Code[Code Commit]
    end
    
    subgraph CI
        Build[Build]
        UnitTest[Unit Tests]
        Lint[Linting]
        Security[Security Scan]
    end
    
    subgraph Artifacts
        Registry[(Container Registry)]
    end
    
    subgraph CD
        DeployDev[Deploy to Dev]
        IntTest[Integration Tests]
        DeployStaging[Deploy to Staging]
        E2ETest[E2E Tests]
        DeployProd[Deploy to Production]
    end
    
    Code --> Build
    Build --> UnitTest
    UnitTest --> Lint
    Lint --> Security
    Security --> Registry
    Registry --> DeployDev
    DeployDev --> IntTest
    IntTest --> DeployStaging
    DeployStaging --> E2ETest
    E2ETest --> DeployProd
```

## Deployment Diagram

### Mermaid Version

```mermaid
graph TB
    subgraph Kubernetes Cluster
        subgraph Namespace: Production
            subgraph Deployment: Frontend
                FE1[Pod: frontend-1]
                FE2[Pod: frontend-2]
            end
            
            subgraph Deployment: Backend
                BE1[Pod: backend-1]
                BE2[Pod: backend-2]
                BE3[Pod: backend-3]
            end
            
            subgraph StatefulSet: Database
                DB1[(Pod: postgres-0)]
                DB2[(Pod: postgres-1)]
            end
            
            SvcFE[Service: frontend-svc]
            SvcBE[Service: backend-svc]
            SvcDB[Service: postgres-svc]
            
            Ingress[Ingress Controller]
        end
    end
    
    Ingress --> SvcFE
    SvcFE --> FE1
    SvcFE --> FE2
    FE1 --> SvcBE
    FE2 --> SvcBE
    SvcBE --> BE1
    SvcBE --> BE2
    SvcBE --> BE3
    BE1 --> SvcDB
    BE2 --> SvcDB
    BE3 --> SvcDB
    SvcDB --> DB1
    SvcDB --> DB2
```

## Usage Instructions

### Using Mermaid Diagrams

1. Copy the Mermaid code block
2. Open DrawIO (app.diagrams.net)
3. Go to `Arrange > Insert > Mermaid`
4. Paste the code and click `Insert`
5. The diagram will be created as an editable group

### Using DrawIO XML

1. Copy the XML content
2. Save as a `.drawio` file
3. Open in DrawIO
4. Or paste into a new diagram using `File > Import From > Text`

### Customization Tips

- Change colors by modifying `fillColor` and `strokeColor` values
- Adjust sizes by changing `width` and `height` in geometry
- Reposition by changing `x` and `y` coordinates
- Add new shapes by copying existing `mxCell` elements and changing IDs
