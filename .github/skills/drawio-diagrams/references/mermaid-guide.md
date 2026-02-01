# Mermaid Syntax Guide for DrawIO

Mermaid is a text-based diagramming syntax that DrawIO can import and render. This guide covers all supported diagram types.

## How to Use Mermaid in DrawIO

1. Open DrawIO (app.diagrams.net)
2. Go to: `Arrange > Insert > Mermaid`
3. Paste your Mermaid code
4. Click `Insert`

The diagram will be created as an editable shape group.

## Flowchart / Graph

### Basic Syntax

```mermaid
graph TD
    A[Rectangle] --> B(Rounded)
    B --> C{Diamond}
    C -->|Yes| D[Result 1]
    C -->|No| E[Result 2]
```

### Direction

| Code | Direction |
| --- | --- |
| `graph TD` | Top to Down |
| `graph TB` | Top to Bottom (same as TD) |
| `graph BT` | Bottom to Top |
| `graph LR` | Left to Right |
| `graph RL` | Right to Left |

### Node Shapes

```mermaid
graph LR
    A[Rectangle]
    B(Rounded Rectangle)
    C([Stadium])
    D[[Subroutine]]
    E[(Database)]
    F((Circle))
    G>Asymmetric]
    H{Diamond}
    I{{Hexagon}}
    J[/Parallelogram/]
    K[\Parallelogram Alt\]
    L[/Trapezoid\]
    M[\Trapezoid Alt/]
```

### Link Styles

```mermaid
graph LR
    A --> B
    A --- C
    A -.-> D
    A ==> E
    A --text--> F
    A ---|text| G
    A -.text.-> H
    A ==text==> I
```

| Syntax | Result |
| --- | --- |
| `-->` | Arrow |
| `---` | Line (no arrow) |
| `-.->` | Dotted arrow |
| `==>` | Thick arrow |
| `--text-->` | Arrow with text |
| `-->|text|` | Arrow with text (alt) |

### Subgraphs

```mermaid
graph TB
    subgraph Frontend
        A[Web App]
        B[Mobile App]
    end
    subgraph Backend
        C[API Server]
        D[Worker]
    end
    subgraph Data
        E[(Database)]
        F[(Cache)]
    end
    A --> C
    B --> C
    C --> D
    C --> E
    C --> F
```

## Sequence Diagram

### Basic Syntax

```mermaid
sequenceDiagram
    participant U as User
    participant A as API
    participant D as Database
    
    U->>A: Request
    A->>D: Query
    D-->>A: Results
    A-->>U: Response
```

### Message Types

| Syntax | Description |
| --- | --- |
| `->` | Solid line |
| `-->` | Dotted line |
| `->>` | Solid with arrow |
| `-->>` | Dotted with arrow |
| `-x` | Solid with cross |
| `--x` | Dotted with cross |
| `-)` | Solid with open arrow |
| `--)` | Dotted with open arrow |

### Activations

```mermaid
sequenceDiagram
    participant U as User
    participant A as API
    
    U->>+A: Request
    Note right of A: Processing...
    A-->>-U: Response
```

### Loops and Conditions

```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server
    
    C->>S: Connect
    
    loop Heartbeat
        S->>C: Ping
        C->>S: Pong
    end
    
    alt Success
        S->>C: Data
    else Failure
        S->>C: Error
    end
    
    opt Optional
        C->>S: Acknowledge
    end
```

### Notes

```mermaid
sequenceDiagram
    participant A
    participant B
    
    Note left of A: Note on left
    Note right of B: Note on right
    Note over A: Note over A
    Note over A,B: Note spanning A and B
```

## Class Diagram

### Basic Syntax

```mermaid
classDiagram
    class Animal {
        +String name
        +int age
        +makeSound()
    }
    
    class Dog {
        +String breed
        +bark()
    }
    
    Animal <|-- Dog
```

### Visibility Modifiers

| Symbol | Meaning |
| --- | --- |
| `+` | Public |
| `-` | Private |
| `#` | Protected |
| `~` | Package/Internal |

### Relationships

```mermaid
classDiagram
    classA <|-- classB : Inheritance
    classC *-- classD : Composition
    classE o-- classF : Aggregation
    classG --> classH : Association
    classI -- classJ : Link
    classK ..> classL : Dependency
    classM ..|> classN : Realization
```

| Syntax | Type |
| --- | --- |
| `<\|--` | Inheritance |
| `*--` | Composition |
| `o--` | Aggregation |
| `-->` | Association |
| `--` | Link |
| `..>` | Dependency |
| `..\|>` | Realization |

### Cardinality

```mermaid
classDiagram
    Customer "1" --> "*" Order : places
    Order "1" --> "1..*" LineItem : contains
```

## State Diagram

### Basic Syntax

```mermaid
stateDiagram-v2
    [*] --> Idle
    Idle --> Processing : Start
    Processing --> Completed : Success
    Processing --> Failed : Error
    Completed --> [*]
    Failed --> Idle : Retry
```

### Composite States

```mermaid
stateDiagram-v2
    [*] --> Active
    
    state Active {
        [*] --> Idle
        Idle --> Running
        Running --> Idle
    }
    
    Active --> Inactive
    Inactive --> Active
```

### Forks and Joins

```mermaid
stateDiagram-v2
    state fork_state <<fork>>
    state join_state <<join>>
    
    [*] --> fork_state
    fork_state --> State2
    fork_state --> State3
    State2 --> join_state
    State3 --> join_state
    join_state --> [*]
```

## Entity Relationship Diagram

### Basic Syntax

```mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : places
    ORDER ||--|{ LINE_ITEM : contains
    PRODUCT ||--o{ LINE_ITEM : "is in"
    CUSTOMER {
        string name
        string email
        int id PK
    }
    ORDER {
        int id PK
        date created
        int customer_id FK
    }
```

### Relationship Types

| Syntax | Meaning |
| --- | --- |
| `\|\|` | Exactly one |
| `o\|` | Zero or one |
| `}o` | Zero or more |
| `}\|` | One or more |

### Cardinality Combinations

```mermaid
erDiagram
    A ||--|| B : "one to one"
    C ||--o{ D : "one to many"
    E }o--o{ F : "many to many"
    G ||--o| H : "one to zero-or-one"
```

## C4 Diagrams

### Context Diagram

```mermaid
C4Context
    title System Context Diagram
    
    Person(user, "User", "A user of the system")
    System(system, "System", "The main system")
    System_Ext(external, "External System", "External dependency")
    
    Rel(user, system, "Uses")
    Rel(system, external, "Calls")
```

### Container Diagram

```mermaid
C4Container
    title Container Diagram
    
    Person(user, "User")
    
    Container_Boundary(system, "System") {
        Container(web, "Web App", "React", "Frontend")
        Container(api, "API", "Node.js", "Backend")
        ContainerDb(db, "Database", "PostgreSQL", "Storage")
    }
    
    Rel(user, web, "Uses")
    Rel(web, api, "API calls")
    Rel(api, db, "Reads/Writes")
```

## Gantt Chart

```mermaid
gantt
    title Project Timeline
    dateFormat YYYY-MM-DD
    
    section Planning
    Requirements    :done, req, 2026-01-01, 2026-01-15
    Design         :active, des, 2026-01-10, 2026-01-25
    
    section Development
    Backend        :dev1, after des, 30d
    Frontend       :dev2, after des, 25d
    
    section Testing
    Integration    :test, after dev1, 10d
    UAT            :uat, after test, 7d
```

### Task States

| Keyword | Effect |
| --- | --- |
| `done` | Completed (gray) |
| `active` | In progress |
| `crit` | Critical path |
| `milestone` | Diamond marker |

## Pie Chart

```mermaid
pie title Distribution
    "Category A" : 40
    "Category B" : 30
    "Category C" : 20
    "Category D" : 10
```

## Mindmap

```mermaid
mindmap
    root((Central Topic))
        Branch 1
            Leaf 1.1
            Leaf 1.2
        Branch 2
            Leaf 2.1
            Leaf 2.2
                Sub-leaf 2.2.1
        Branch 3
```

## Git Graph

```mermaid
gitGraph
    commit id: "Initial"
    branch develop
    checkout develop
    commit id: "Feature A"
    commit id: "Feature B"
    checkout main
    merge develop id: "Release 1.0"
    commit id: "Hotfix"
```

## Journey Diagram

```mermaid
journey
    title User Journey
    section Sign Up
        Visit site: 5: User
        Fill form: 3: User
        Verify email: 4: User
    section First Use
        Login: 5: User
        Complete tutorial: 4: User
        Create first item: 3: User
```

## Styling

### Node Styling

```mermaid
graph LR
    A:::highlight --> B
    B --> C:::warning
    
    classDef highlight fill:#f9f,stroke:#333,stroke-width:2px
    classDef warning fill:#ff0,stroke:#f00
```

### Link Styling

```mermaid
graph LR
    A --> B
    linkStyle 0 stroke:#ff0,stroke-width:4px
```

## Best Practices

1. **Keep it simple**: Start with basic diagrams, add detail as needed
2. **Use aliases**: `participant U as User` makes code more readable
3. **Add notes**: Use notes to explain complex interactions
4. **Group related items**: Use subgraphs for logical grouping
5. **Consistent direction**: Pick TD or LR and stick with it
6. **Meaningful IDs**: Use descriptive node IDs like `webServer` not `A`

## Common Patterns

### API Flow

```mermaid
sequenceDiagram
    participant C as Client
    participant LB as Load Balancer
    participant API as API Server
    participant DB as Database
    participant Cache as Redis
    
    C->>LB: Request
    LB->>API: Forward
    API->>Cache: Check cache
    alt Cache hit
        Cache-->>API: Cached data
    else Cache miss
        API->>DB: Query
        DB-->>API: Data
        API->>Cache: Store
    end
    API-->>LB: Response
    LB-->>C: Response
```

### Microservices

```mermaid
graph TB
    subgraph Frontend
        Web[Web App]
        Mobile[Mobile App]
    end
    
    subgraph Gateway
        API[API Gateway]
    end
    
    subgraph Services
        Auth[Auth Service]
        User[User Service]
        Order[Order Service]
        Payment[Payment Service]
    end
    
    subgraph Data
        AuthDB[(Auth DB)]
        UserDB[(User DB)]
        OrderDB[(Order DB)]
    end
    
    Web --> API
    Mobile --> API
    API --> Auth
    API --> User
    API --> Order
    API --> Payment
    Auth --> AuthDB
    User --> UserDB
    Order --> OrderDB
```

### State Machine

```mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> Pending : Submit
    Pending --> Approved : Approve
    Pending --> Rejected : Reject
    Rejected --> Draft : Revise
    Approved --> Published : Publish
    Published --> Archived : Archive
    Archived --> [*]
```
