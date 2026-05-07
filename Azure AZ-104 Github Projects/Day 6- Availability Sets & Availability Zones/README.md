# AZ-104 Hands-On Project – Day 06: Availability Sets and Availability Zones

## Project Overview

This is Day 06 of my 30-day scenario-based AZ-104 Azure Administrator hands-on project series.

In this lab, I implemented two Azure virtual machine high-availability patterns:

1. Availability Sets
2. Availability Zones

The goal was to understand how Azure improves VM resilience against hardware failure, planned maintenance, and datacenter-level outages.

---

## Scenario

A company wants to host a business-critical web application in Azure. As the Azure Administrator, I was asked to design and deploy resilient VM infrastructure using Availability Sets and Availability Zones.

---

## What I Built

### Availability Set Design

- Created an Availability Set.
- Deployed two VMs into the Availability Set.
- Validated VM grouping.
- Reviewed fault domain and update domain concepts.

### Availability Zone Design

- Deployed one VM in Zone 1.
- Deployed one VM in Zone 2.
- Validated zone placement.
- Compared zone resilience with Availability Set resilience.

---

## Technologies Used

- Microsoft Azure
- Azure Virtual Machines
- Availability Sets
- Availability Zones
- Azure Virtual Network
- Network Security Groups
- Azure CLI
- Azure Portal

---

## Architecture

```text
Resource Group: rg-az104-dev-uks-001

Availability Set Design
-----------------------
Availability Set: avset-web-uks-001
   |
   |-- vm-avset-web-01
   |-- vm-avset-web-02

Availability Zone Design
------------------------
Zone 1
   |
   |-- vm-zone-web-01

Zone 2
   |
   |-- vm-zone-web-02
```

---

## Key Learning Outcomes

By completing this lab, I practised:

- Creating Availability Sets.
- Deploying VMs into Availability Sets.
- Deploying VMs into Availability Zones.
- Understanding fault domains.
- Understanding update domains.
- Comparing Availability Sets and Availability Zones.
- Validating resilient VM placement.

---

## Azure Best Practice Notes

- Use Availability Zones for stronger resilience where supported.
- Use Availability Sets when zones are unavailable or when lower-latency grouping is required.
- Choose availability configuration during VM creation.
- Avoid single-instance designs for production workloads.
- Deallocate lab VMs when not in use to reduce cost.
- Use load balancing in future designs to distribute traffic across resilient VMs.

---

## Evidence

Store screenshots in:

```text
/images/day06/
```

Recommended evidence:

- Availability Set overview.
- VMs inside Availability Set.
- VM deployed in Zone 1.
- VM deployed in Zone 2.
- CLI validation output.
- Deallocated VM status after the lab.

---

## Cleanup / Cost Control

To reduce compute cost:

```bash
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-avset-web-01
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-avset-web-02
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-zone-web-01
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-zone-web-02
```

---

## Official Azure References

- Availability Sets overview: https://learn.microsoft.com/en-us/azure/virtual-machines/availability-set-overview
- Availability options for Azure Virtual Machines: https://learn.microsoft.com/en-us/azure/virtual-machines/availability
- Azure Availability Zones overview: https://learn.microsoft.com/en-us/azure/reliability/availability-zones-overview
- Reliability in Azure Virtual Machines: https://learn.microsoft.com/en-us/azure/reliability/reliability-virtual-machines
- Azure Well-Architected Framework: https://learn.microsoft.com/en-us/azure/well-architected/
