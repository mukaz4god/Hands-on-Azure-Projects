# Day 10 – Private vs Public IP: Secure VM Access with Azure Bastion

## Scenario

Your organisation has completed a security review of its Azure environment. The review found that some virtual machines may be directly exposed to the internet through public IP addresses and management ports such as RDP or SSH.

This is a common cloud security risk.

As the Azure Administrator, your task is to redesign administrative access so VMs can be managed privately without exposing RDP or SSH directly to the internet.

You will use Azure Bastion as the recommended secure access method.

## Business Requirements

The business requires secure administrator access to Azure VMs without exposing management ports to the public internet.

The solution must:

- Remove direct public IP access from VMs where possible.
- Use private IP-based administration.
- Deploy Azure Bastion.
- Allow browser-based RDP/SSH through Azure Portal.
- Reduce attack surface.
- Support security audit requirements.

## Technical Requirements

Create or configure:

| Resource | Purpose |
|---|---|
| `AzureBastionSubnet` | Required subnet for Azure Bastion |
| Azure Bastion host | Secure browser-based VM access |
| Bastion Public IP | Public endpoint for Bastion service |
| Private VM access | RDP/SSH through Bastion |
| NSG review | Avoid direct RDP/SSH exposure |

## Architecture

Before:

```text
Internet
   |
Public IP
   |
VM
   |
RDP/SSH exposed
```

After:

```text
Administrator Browser
   |
Azure Portal / Bastion
   |
Azure Bastion
   |
Private IP
   |
VM without public IP
```

## Why This Matters

Directly exposing management ports is one of the most common cloud risks.

Examples:

```text
RDP 3389 exposed to Internet
SSH 22 exposed to Internet
WinRM 5985/5986 exposed to Internet
```

Azure Bastion helps reduce this risk by providing secure access over TLS through the Azure Portal.

## AZ-104 Skills Covered

- Understand public IP vs private IP.
- Configure Azure Bastion.
- Create `AzureBastionSubnet`.
- Connect to VMs using Bastion.
- Remove or avoid public IPs on VMs.
- Secure VM administration.
- Understand production-grade access patterns.

## Azure Well-Architected Framework Alignment

| Pillar | How this lab supports it |
|---|---|
| Security | Removes direct public management exposure. |
| Reliability | Centralised access method reduces inconsistent configurations. |
| Operational Excellence | Bastion simplifies secure access through Azure Portal. |
| Cost Optimization | Includes cleanup guidance because Bastion incurs cost. |
| Performance Efficiency | Private access avoids unnecessary public exposure. |

## Success Criteria

You should be able to show:

- `AzureBastionSubnet` exists.
- Azure Bastion is deployed.
- VM can be accessed using Bastion.
- VM does not require direct public IP access.
- You can explain why public management ports are risky.
