# Cloud Shape Reference for DrawIO

This document provides shape identifiers and styles for major cloud providers in DrawIO diagrams.

## AWS Shapes

AWS shape libraries are available in DrawIO under: `More Shapes > Networking > AWS`

### Compute

| Service | Shape Name | Style Prefix |
| --- | --- | --- |
| EC2 | AWS EC2 | `outlineConnect=0;dashed=0;verticalLabelPosition=bottom;` |
| Lambda | AWS Lambda | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| ECS | AWS ECS | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| EKS | AWS EKS | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Fargate | AWS Fargate | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Batch | AWS Batch | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Elastic Beanstalk | AWS Elastic Beanstalk | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |

### Storage

| Service | Shape Name | Style Prefix |
| --- | --- | --- |
| S3 | AWS S3 | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| EBS | AWS EBS | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| EFS | AWS EFS | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Glacier | AWS Glacier | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Storage Gateway | AWS Storage Gateway | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |

### Database

| Service | Shape Name | Style Prefix |
| --- | --- | --- |
| RDS | AWS RDS | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| DynamoDB | AWS DynamoDB | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Aurora | AWS Aurora | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| ElastiCache | AWS ElastiCache | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Redshift | AWS Redshift | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| DocumentDB | AWS DocumentDB | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |

### Networking

| Service | Shape Name | Style Prefix |
| --- | --- | --- |
| VPC | AWS VPC | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Route 53 | AWS Route 53 | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| CloudFront | AWS CloudFront | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| API Gateway | AWS API Gateway | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| ELB/ALB | AWS Elastic Load Balancing | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Direct Connect | AWS Direct Connect | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |

### Integration

| Service | Shape Name | Style Prefix |
| --- | --- | --- |
| SQS | AWS SQS | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| SNS | AWS SNS | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| EventBridge | AWS EventBridge | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Step Functions | AWS Step Functions | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| AppSync | AWS AppSync | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |

### Security

| Service | Shape Name | Style Prefix |
| --- | --- | --- |
| IAM | AWS IAM | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Cognito | AWS Cognito | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| KMS | AWS KMS | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| WAF | AWS WAF | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Shield | AWS Shield | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |
| Secrets Manager | AWS Secrets Manager | `sketch=0;outlineConnect=0;fontColor=#232F3E;` |

### AWS Group Shapes

| Group | Purpose |
| --- | --- |
| AWS Cloud | Entire AWS environment |
| Region | AWS Region boundary |
| Availability Zone | AZ boundary |
| VPC | Virtual Private Cloud |
| Subnet (Public) | Public subnet |
| Subnet (Private) | Private subnet |
| Security Group | Security group boundary |

## Azure Shapes

Azure shape libraries are available under: `More Shapes > Networking > Azure`

### Compute

| Service | Shape Name |
| --- | --- |
| Virtual Machine | Azure VM |
| App Service | Azure App Service |
| Functions | Azure Functions |
| AKS | Azure Kubernetes Service |
| Container Instances | Azure Container Instances |
| Batch | Azure Batch |

### Storage

| Service | Shape Name |
| --- | --- |
| Blob Storage | Azure Blob Storage |
| File Storage | Azure File Storage |
| Queue Storage | Azure Queue Storage |
| Table Storage | Azure Table Storage |
| Disk Storage | Azure Managed Disks |
| Data Lake | Azure Data Lake |

### Database

| Service | Shape Name |
| --- | --- |
| SQL Database | Azure SQL Database |
| Cosmos DB | Azure Cosmos DB |
| MySQL | Azure Database for MySQL |
| PostgreSQL | Azure Database for PostgreSQL |
| Cache for Redis | Azure Cache for Redis |
| Synapse Analytics | Azure Synapse Analytics |

### Networking

| Service | Shape Name |
| --- | --- |
| Virtual Network | Azure VNet |
| Load Balancer | Azure Load Balancer |
| Application Gateway | Azure Application Gateway |
| Traffic Manager | Azure Traffic Manager |
| CDN | Azure CDN |
| ExpressRoute | Azure ExpressRoute |
| Front Door | Azure Front Door |

### Integration

| Service | Shape Name |
| --- | --- |
| Service Bus | Azure Service Bus |
| Event Hubs | Azure Event Hubs |
| Event Grid | Azure Event Grid |
| Logic Apps | Azure Logic Apps |
| API Management | Azure API Management |

### Security

| Service | Shape Name |
| --- | --- |
| Active Directory | Azure AD |
| Key Vault | Azure Key Vault |
| Security Center | Azure Security Center |
| Sentinel | Azure Sentinel |

### Azure Group Shapes

| Group | Purpose |
| --- | --- |
| Azure | Azure cloud boundary |
| Subscription | Subscription container |
| Resource Group | Resource group container |
| Virtual Network | VNet boundary |
| Subnet | Subnet boundary |
| Availability Zone | AZ boundary |

## GCP Shapes

GCP shape libraries are available under: `More Shapes > Networking > GCP`

### Compute

| Service | Shape Name |
| --- | --- |
| Compute Engine | GCP Compute Engine |
| App Engine | GCP App Engine |
| Cloud Functions | GCP Cloud Functions |
| Cloud Run | GCP Cloud Run |
| GKE | GCP GKE |
| Anthos | GCP Anthos |

### Storage

| Service | Shape Name |
| --- | --- |
| Cloud Storage | GCP Cloud Storage |
| Persistent Disk | GCP Persistent Disk |
| Filestore | GCP Filestore |
| Archive Storage | GCP Archive |

### Database

| Service | Shape Name |
| --- | --- |
| Cloud SQL | GCP Cloud SQL |
| Cloud Spanner | GCP Spanner |
| Bigtable | GCP Bigtable |
| Firestore | GCP Firestore |
| Memorystore | GCP Memorystore |
| BigQuery | GCP BigQuery |

### Networking

| Service | Shape Name |
| --- | --- |
| VPC | GCP VPC |
| Cloud Load Balancing | GCP Load Balancer |
| Cloud CDN | GCP Cloud CDN |
| Cloud DNS | GCP Cloud DNS |
| Cloud Interconnect | GCP Cloud Interconnect |
| Cloud NAT | GCP Cloud NAT |

### Integration

| Service | Shape Name |
| --- | --- |
| Pub/Sub | GCP Pub/Sub |
| Cloud Tasks | GCP Cloud Tasks |
| Cloud Scheduler | GCP Cloud Scheduler |
| Workflows | GCP Workflows |
| Eventarc | GCP Eventarc |

### GCP Group Shapes

| Group | Purpose |
| --- | --- |
| Google Cloud | GCP cloud boundary |
| Project | Project container |
| Region | Region boundary |
| Zone | Zone boundary |
| VPC | VPC boundary |

## Generic Infrastructure Shapes

These work across all cloud providers:

### Compute Generic

| Shape | Style |
| --- | --- |
| Server | `shape=cube;whiteSpace=wrap;html=1;` |
| Container | `shape=mxgraph.kubernetes.container;` |
| VM | `shape=image;aspect=fixed;image=...` |
| Function | `shape=mxgraph.aws4.lambda_function;` |

### Network Generic

| Shape | Style |
| --- | --- |
| Load Balancer | `shape=mxgraph.inficons.load_balancer;` |
| Firewall | `shape=mxgraph.cisco.security.firewall;` |
| Router | `shape=mxgraph.cisco.routers.router;` |
| CDN | `shape=mxgraph.aws4.cloudfront;` |

### Storage Generic

| Shape | Style |
| --- | --- |
| Database | `shape=cylinder3;whiteSpace=wrap;html=1;` |
| Cache | `shape=mxgraph.inficons.cache;` |
| Queue | `shape=mxgraph.aws4.sqs;` |
| Object Storage | `shape=mxgraph.aws4.s3;` |

### Users and Clients

| Shape | Style |
| --- | --- |
| User | `shape=mxgraph.azure.user;` |
| Mobile Client | `shape=mxgraph.ios7.icons.smartphone;` |
| Desktop Client | `shape=mxgraph.mockup.devices.iMac;` |
| Web Browser | `shape=mxgraph.mockup.devices.browser_window;` |

## Architecture Patterns

### Three-Tier Architecture

```text
                    ┌─────────────────┐
                    │  Load Balancer  │
                    └────────┬────────┘
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
┌───────▼───────┐    ┌───────▼───────┐    ┌───────▼───────┐
│   Web Server  │    │   Web Server  │    │   Web Server  │
└───────┬───────┘    └───────┬───────┘    └───────┬───────┘
        │                    │                    │
        └────────────────────┼────────────────────┘
                             │
                    ┌────────▼────────┐
                    │   Application   │
                    │     Server      │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │    Database     │
                    │   (Primary)     │
                    └─────────────────┘
```

### Microservices Pattern

```text
┌─────────────────────────────────────────────────────────────┐
│                        API Gateway                          │
└─────────┬─────────┬─────────┬─────────┬─────────┬──────────┘
          │         │         │         │         │
    ┌─────▼────┐ ┌──▼───┐ ┌───▼──┐ ┌────▼───┐ ┌───▼────┐
    │  Auth    │ │ User │ │Order │ │Payment │ │Inventory│
    │ Service  │ │  Svc │ │  Svc │ │   Svc  │ │   Svc   │
    └────┬─────┘ └──┬───┘ └───┬──┘ └────┬───┘ └───┬────┘
         │         │         │         │         │
    ┌────▼─┐   ┌───▼──┐  ┌───▼──┐  ┌───▼──┐  ┌───▼──┐
    │AuthDB│   │UserDB│  │OrderDB│ │PayDB │  │InvDB │
    └──────┘   └──────┘  └──────┘  └──────┘  └──────┘
```

### Event-Driven Architecture

```text
┌─────────┐      ┌─────────────┐      ┌─────────┐
│ Producer├─────►│   Message   ├─────►│Consumer │
└─────────┘      │    Bus      │      └─────────┘
                 │  (Kafka/    │      ┌─────────┐
┌─────────┐      │  EventHub)  ├─────►│Consumer │
│ Producer├─────►│             │      └─────────┘
└─────────┘      └─────────────┘      ┌─────────┐
                                      │Consumer │
                                      └─────────┘
```

## Color Recommendations

### AWS

- Primary: `#FF9900` (Orange)
- Secondary: `#232F3E` (Dark Blue)
- Accent: `#1A73E8` (Blue)

### Azure

- Primary: `#0089D6` (Azure Blue)
- Secondary: `#68217A` (Purple)
- Accent: `#50E6FF` (Light Blue)

### GCP

- Primary: `#4285F4` (Google Blue)
- Secondary: `#EA4335` (Red)
- Tertiary: `#FBBC05` (Yellow)
- Quaternary: `#34A853` (Green)

### Generic

- Compute: `#FF6B6B` (Coral)
- Storage: `#4ECDC4` (Teal)
- Database: `#45B7D1` (Blue)
- Network: `#96CEB4` (Green)
- Security: `#FFEAA7` (Yellow)
