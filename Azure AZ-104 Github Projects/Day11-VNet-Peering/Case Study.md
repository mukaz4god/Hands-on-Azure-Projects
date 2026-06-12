# Day 11 – Enterprise Networking Lab: VNet Peering

## Scenario

Your organisation has two application teams working in separate Azure network environments.

- The **Web Team** hosts frontend services in one virtual network.
- The **Data/API Team** hosts backend services in another virtual network.

For security and operational separation, each team uses its own VNet. However, the applications must communicate privately over the Microsoft backbone without using public IP addresses or the internet.

As the Azure Administrator, your task is to create two VNets, peer them together, and validate private connectivity.

---

## Business Requirements

The business requires secure private communication between two Azure VNets.

The solution must:

- Create two separate virtual networks.
- Ensure IP address spaces do not overlap.
- Configure bidirectional VNet peering.
- Test private connectivity between workloads.
- Avoid public internet routing for private application communication.
- Use clear naming conventions.
- Support future enterprise network expansion.

---

## Technical Requirements

Create:

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| VNet 1 | `vnet-web-uks-001` |
| VNet 1 address space | `10.11.0.0/16` |
| VNet 1 subnet | `subnet-web` |
| VNet 1 subnet prefix | `10.11.1.0/24` |
| VNet 2 | `vnet-app-uks-001` |
| VNet 2 address space | `10.12.0.0/16` |
| VNet 2 subnet | `subnet-app` |
| VNet 2 subnet prefix | `10.12.1.0/24` |
| Peering 1 | `peer-web-to-app` |
| Peering 2 | `peer-app-to-web` |

Optional test resources:

| Resource | Purpose |
|---|---|
| `vm-web-peer-01` | Test VM in web VNet |
| `vm-app-peer-01` | Test VM in app VNet |

---

## Architecture

```text
Resource Group: rg-az104-dev-uks-001

VNet: vnet-web-uks-001
Address Space: 10.11.0.0/16
Subnet: subnet-web 10.11.1.0/24
        |
        | VNet Peering
        |
VNet: vnet-app-uks-001
Address Space: 10.12.0.0/16
Subnet: subnet-app 10.12.1.0/24
```

Traffic between the VNets stays on the Azure backbone and does not require public IPs.

---

## Why This Matters in Enterprise Environments

Enterprise Azure environments commonly use separate VNets for:

- Application tiers
- Business units
- Landing zones
- Shared services
- Security boundaries
- Hub-and-spoke designs

VNet peering is a common method for connecting these networks privately.

---

## Key Design Rules

### Address spaces must not overlap

Bad:

```text
VNet A: 10.0.0.0/16
VNet B: 10.0.0.0/16
```

Good:

```text
VNet A: 10.11.0.0/16
VNet B: 10.12.0.0/16
```

### Peering is not transitive

If VNet A peers with VNet B and VNet B peers with VNet C, VNet A does not automatically communicate with VNet C.

For transitive routing, enterprises typically use a hub-and-spoke design with Azure Firewall, VPN Gateway, Route Server, or Network Virtual Appliances.

---

## AZ-104 Skills Covered

- Create Azure virtual networks.
- Configure VNet address spaces.
- Configure subnets.
- Configure VNet peering.
- Validate peering status.
- Understand non-overlapping IP ranges.
- Understand transitive routing limitations.
- Test private connectivity.
- Understand hub-and-spoke network concepts.

---

## Azure Well-Architected Framework Alignment

| Pillar | How this lab supports it |
|---|---|
| Security | Enables private connectivity without public IPs. |
| Reliability | Uses Azure backbone connectivity between VNets. |
| Operational Excellence | Separates workloads into clear network boundaries. |
| Cost Optimization | Avoids unnecessary VPN gateways for simple VNet-to-VNet communication. |
| Performance Efficiency | Low-latency private connectivity across Azure network. |

---

## Success Criteria

You should be able to demonstrate:

- Two VNets created with non-overlapping address spaces.
- Peering exists from web VNet to app VNet.
- Peering exists from app VNet to web VNet.
- Peering status shows `Connected`.
- Test VM in one VNet can privately reach test VM in the other VNet.
- You can explain why peering is not transitive.
