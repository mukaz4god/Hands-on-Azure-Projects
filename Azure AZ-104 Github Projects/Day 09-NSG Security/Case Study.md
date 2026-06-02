# Day 09 – Network Security Groups (NSG): Secure Web and App Subnets

## Scenario

Your organisation has created a multi-tier Azure network foundation for a future application.

The network currently contains:

```text
VNet: vnet-az104-core-uks-001
Address space: 10.0.0.0/16

subnet-web: 10.0.1.0/24
subnet-app: 10.0.2.0/24
```

A security review has found that there is no explicit subnet-level filtering in place. The security team has asked you, as the Azure Administrator, to implement Network Security Groups using least privilege principles.

The web subnet should only allow HTTP traffic from the internet. The app subnet should not be directly accessible from the internet.

## Business Requirements

The business requires a secure network design that:

- Allows web traffic to the web tier.
- Blocks unnecessary inbound access.
- Prevents direct internet access to the app tier.
- Supports future multi-tier application deployment.
- Follows least privilege and defence-in-depth principles.
- Can be clearly documented for audit and operations.

## Technical Requirements

Create and configure:

| Resource | Purpose |
|---|---|
| `nsg-web-001` | Protects `subnet-web` |
| `nsg-app-001` | Protects `subnet-app` |
| Allow HTTP rule | Allows TCP 80 to web subnet |
| Deny internet inbound rule | Explicitly denies unwanted internet traffic |
| Subnet associations | Attach NSGs to correct subnets |

## Security Design

```text
Internet
   |
   | Allow TCP 80 only
   |
nsg-web-001
   |
subnet-web: 10.0.1.0/24


Internet
   |
   | Denied
   |
nsg-app-001
   |
subnet-app: 10.0.2.0/24
```

## AppSec Perspective

This lab mirrors a common cloud security review finding:

> “Management or application ports are overly exposed to the internet.”

Examples of risky rules:

```text
Source: Internet
Destination: Any
Port: Any
Action: Allow
```

A better approach is:

```text
Source: Internet
Destination: Web tier
Port: 80 or 443 only
Action: Allow
```

This reduces attack surface and supports defence in depth.

## AZ-104 Skills Covered

- Create Network Security Groups.
- Configure inbound security rules.
- Understand NSG rule priority.
- Associate NSGs with subnets.
- Validate subnet-level network security.
- Understand default Azure NSG rules.
- Apply least privilege networking.

## Azure Well-Architected Framework Alignment

| Pillar | How this lab supports it |
|---|---|
| Security | Restricts traffic to required ports only. |
| Reliability | Clear network rules reduce accidental exposure and misconfiguration. |
| Operational Excellence | Subnet-level NSGs provide consistent policy enforcement. |
| Cost Optimization | NSGs are native controls and do not add major cost. |
| Performance Efficiency | Simple subnet-based rules reduce operational complexity. |

## Success Criteria

You should be able to demonstrate:

- `nsg-web-001` exists.
- `nsg-app-001` exists.
- Web subnet allows HTTP only from the internet.
- App subnet blocks direct internet inbound traffic.
- NSGs are associated with the correct subnets.
- You can explain rule priority and default deny behaviour.
