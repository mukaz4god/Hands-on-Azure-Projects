# Day 06 – Azure Compute Resilience Lab: Availability Sets and Availability Zones

## Scenario

An organisation is preparing to host a business-critical internal web application in Azure. The application must remain available during planned Azure host maintenance and must also be resilient against hardware or datacenter-level failures.

As the Azure Administrator, your task is to deploy and compare two Azure VM high-availability patterns:

1. **Availability Sets** – to protect workloads from rack-level hardware failures and planned host maintenance.
2. **Availability Zones** – to protect workloads from datacenter-level failures inside a supported Azure region.

This lab is designed to help me prove my AZ-104 knowledge in a practical, scenario-based way.

---

## Business Requirements

The organisation requires a resilient compute design for a web workload.

The solution must:

- Deploy two VMs inside an Availability Set.
- Deploy two VMs into different Availability Zones.
- Demonstrate understanding of fault domains and update domains.
- Demonstrate understanding of zone-level resilience.
- Use clear naming conventions.
- Include validation evidence for GitHub.
- Include cleanup guidance to control cost.

---

## Technical Requirements

### Part A – Availability Set Deployment

You will create:

- One Availability Set.
- Two Windows Server VMs in the Availability Set.
- A dedicated VNet and subnet.
- An NSG allowing RDP for lab access.
- Validation that both VMs are associated with the Availability Set.

### Part B – Availability Zone Deployment

You will create:

- One Windows Server VM in Zone 1.
- One Windows Server VM in Zone 2.
- Validation that each VM is deployed into the correct zone.

---

## Existing Environment

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| Region | `uksouth` |
| Lab VNet | `vnet-ha-uks-001` |
| Lab Subnet | `subnet-ha-web` |

---

## Architecture Overview

```text
Resource Group: rg-az104-dev-uks-001

Part A – Availability Set
-------------------------
Availability Set: avset-web-uks-001
   |
   |-- vm-avset-web-01
   |-- vm-avset-web-02

Azure spreads VMs across:
- Fault domains
- Update domains


Part B – Availability Zones
---------------------------
Availability Zone 1
   |
   |-- vm-zone-web-01

Availability Zone 2
   |
   |-- vm-zone-web-02
```

---

## Key Concepts

### Availability Set

An Availability Set is a logical grouping of VMs that helps keep applications available during hardware failures and planned Azure maintenance.

Availability Sets use:

- **Fault domains** – separate physical hardware groups, such as racks, power, and network equipment.
- **Update domains** – groups of VMs that may be rebooted together during planned maintenance.

### Availability Zone

An Availability Zone is a physically separate datacenter location inside an Azure region. Availability Zones provide stronger resilience than Availability Sets because they protect against datacenter-level failures.

---

## AZ-104 Skills Covered

- Deploy and manage Azure virtual machines.
- Configure virtual machine availability options.
- Deploy VMs into Availability Sets.
- Deploy VMs across Availability Zones.
- Understand fault domains and update domains.
- Understand region and zone availability.
- Validate Azure VM resilience configuration.

---

## Azure Well-Architected Framework Alignment

| Pillar | How this lab supports it |
|---|---|
| Reliability | Uses Availability Sets and Zones to reduce single points of failure. |
| Security | Uses NSGs to control inbound management access. |
| Cost Optimization | Uses small VM SKUs and includes deallocation steps. |
| Operational Excellence | Uses consistent naming, validation, and documentation. |
| Performance Efficiency | Compares VM placement patterns and when to use each. |

---

## Success Criteria

By the end of this lab, you should be able to prove:

- Two VMs were deployed into one Availability Set.
- You understand fault domains and update domains.
- Two VMs were deployed across different Availability Zones.
- You understand the difference between Availability Sets and Availability Zones.
- You captured evidence for GitHub.
- You can explain this design in an interview.
