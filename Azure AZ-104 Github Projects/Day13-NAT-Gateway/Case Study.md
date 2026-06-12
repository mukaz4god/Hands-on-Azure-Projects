# Day 13 – Enterprise Networking Lab: NAT Gateway + Secure Outbound Connectivity

## Scenario

Your organisation has hardened its Azure virtual machines by removing direct public IP addresses. This improves security because the VMs are no longer directly reachable from the internet.

However, the application servers still need outbound internet access for legitimate tasks such as:

- Downloading operating system updates
- Pulling packages from trusted repositories
- Accessing external APIs
- Sending telemetry to monitoring platforms

The security team requires a design where VMs remain private but can still initiate outbound internet connections through a controlled and predictable outbound path.

As the Azure Administrator, your task is to configure Azure NAT Gateway for secure outbound connectivity.

---

## Business Requirements

The business requires private virtual machines to access the internet outbound without assigning public IP addresses directly to each VM.

The solution must:

- Remove public IP dependency from backend VMs.
- Provide outbound internet connectivity through NAT Gateway.
- Associate NAT Gateway with the workload subnet.
- Use a static public IP for predictable outbound egress.
- Validate outbound connectivity from a private VM.
- Follow enterprise security and cost best practices.

---

## Technical Requirements

Create and configure:

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| VNet | `vnet-nat-uks-001` |
| Subnet | `subnet-private` |
| NSG | `nsg-nat-private-001` |
| Test VM | `vm-nat-private-01` |
| NAT Public IP | `pip-nat-uks-001` |
| NAT Gateway | `natgw-uks-001` |

---

## Architecture

```text
Private VM
vm-nat-private-01
No Public IP
   |
Private IP only
   |
subnet-private
   |
NAT Gateway: natgw-uks-001
   |
Static Public IP: pip-nat-uks-001
   |
Internet outbound only
```

---

## Security Design

Before:

```text
Internet
   |
Public IP
   |
VM
```

Risk:

- VM is directly addressable from the internet.
- Management ports may be exposed.
- Larger attack surface.

After:

```text
VM without public IP
   |
NAT Gateway
   |
Outbound internet access
```

Benefit:

- VM is not directly reachable from the internet.
- Outbound connectivity is centralized.
- Public outbound IP is predictable.
- Better for firewall allowlisting and monitoring.

---

## AppSec and Cloud Security Perspective

This lab directly supports cloud security hardening.

Common finding:

```text
Virtual machine has a public IP address attached.
```

Better design:

```text
Private VM + NAT Gateway for outbound access
```

This reduces exposure while maintaining required outbound connectivity.

---

## AZ-104 Skills Covered

- Configure Azure NAT Gateway.
- Configure outbound connectivity.
- Understand public vs private IP behavior.
- Associate NAT Gateway to a subnet.
- Remove direct public IP dependency from VMs.
- Validate outbound connectivity.
- Understand secure egress design.
- Understand subnet-level NAT behavior.

---

## Azure Well-Architected Framework Alignment

| Pillar | How this lab supports it |
|---|---|
| Security | Removes direct public IP exposure from VMs. |
| Reliability | Provides managed outbound connectivity at subnet level. |
| Operational Excellence | Centralizes outbound access and simplifies troubleshooting. |
| Cost Optimization | Uses one outbound public IP instead of multiple VM public IPs. |
| Performance Efficiency | NAT Gateway scales outbound SNAT more effectively than VM-level public IPs. |

---

## Success Criteria

You should be able to demonstrate:

- VM has no public IP.
- NAT Gateway exists.
- NAT Gateway is associated with the private subnet.
- VM can access the internet outbound.
- Outbound public IP matches the NAT Gateway public IP.
- You can explain why NAT Gateway improves secure outbound design.
