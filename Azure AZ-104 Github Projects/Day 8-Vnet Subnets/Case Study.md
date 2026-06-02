# Day 08 – Azure Networking Lab: Virtual Network and Subnet Design

## Scenario

Your organisation is preparing to deploy a multi-tier application in Azure. Before any application workloads are deployed, the cloud networking foundation must be designed properly.

The application will have two logical tiers:

- **Web tier** – for future public-facing web servers
- **Application tier** – for future internal application servers

As the Azure Administrator, your task is to create a Virtual Network with separate subnets for each tier. This design supports network segmentation, future NSG rules, and a cleaner security model.

## Business Requirements

The business needs a secure and scalable Azure network foundation that can support future application deployments.

The network must:

- Use one Virtual Network.
- Use separate subnets for web and application workloads.
- Support future NSG rules.
- Support future load balancing and private communication.
- Use clear naming conventions.
- Use tags for governance and cost tracking.

## Technical Requirements

| Resource | Value |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| Region | `UK South` |
| Virtual Network | `vnet-az104-core-uks-001` |
| VNet Address Space | `10.0.0.0/16` |
| Web Subnet | `subnet-web` |
| Web Subnet Prefix | `10.0.1.0/24` |
| App Subnet | `subnet-app` |
| App Subnet Prefix | `10.0.2.0/24` |

## Architecture

```text
Virtual Network: vnet-az104-core-uks-001
Address Space: 10.0.0.0/16

├── subnet-web
│   └── 10.0.1.0/24
│   └── Future use: web servers and frontend services
│
└── subnet-app
    └── 10.0.2.0/24
    └── Future use: internal application servers
```

## Why This Design Matters

A flat network where all resources are deployed into one subnet is harder to secure and manage.

By separating workloads into different subnets, you can:

- Apply different NSG rules per tier.
- Control traffic between web and app workloads.
- Prepare for private endpoints and internal services.
- Improve security and operational clarity.
- Create a realistic enterprise network design.

## AZ-104 Skills Covered

- Create and configure Azure Virtual Networks.
- Configure subnet address ranges.
- Understand CIDR notation.
- Understand Azure private IP address allocation.
- Plan IP addressing for cloud workloads.
- Prepare for NSG and routing configuration.

## Azure Well-Architected Framework Alignment

| Pillar | How this lab supports it |
|---|---|
| Security | Subnet segmentation prepares for tier-based access control. |
| Reliability | Proper IP planning avoids future address conflicts. |
| Operational Excellence | Clear naming and documentation improve maintainability. |
| Cost Optimization | Network foundation is low cost and reusable. |
| Performance Efficiency | Logical subnet design supports scalable workload placement. |

## Success Criteria

You should be able to prove that:

- The VNet was created with address space `10.0.0.0/16`.
- `subnet-web` exists with prefix `10.0.1.0/24`.
- `subnet-app` exists with prefix `10.0.2.0/24`.
- Tags were applied.
- You can explain why subnet segmentation matters.
