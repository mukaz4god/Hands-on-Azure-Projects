# AZ-104 Hands-On Project – Day 08: VNet and Subnets

## Project Overview

This is Day 08 of my 30-day scenario-based AZ-104 Azure Administrator project series.

In this lab, I created a foundational Azure Virtual Network with separate subnets for web and application workloads.

## Scenario

A company is preparing to deploy a multi-tier application in Azure. Before deploying workloads, the Azure network must be designed with clear segmentation between the web tier and application tier.

## What I Built

- Azure Virtual Network
- Web subnet
- App subnet
- IP address plan
- Governance tags

## Architecture

```text
vnet-az104-core-uks-001
Address Space: 10.0.0.0/16

├── subnet-web
│   └── 10.0.1.0/24
│
└── subnet-app
    └── 10.0.2.0/24
```

## Skills Practised

- Azure Virtual Network creation
- Subnet planning
- CIDR notation
- IP address segmentation
- Azure Portal networking configuration
- Azure CLI networking configuration
- Tagging and governance

## Azure Best Practice Notes

- Separate workloads into different subnets.
- Avoid overlapping address spaces.
- Plan IP ranges before deployment.
- Leave room for future growth.
- Use tags for ownership and cost tracking.
- Prepare subnet boundaries before applying NSG rules.

## Evidence

Store screenshots in:

```text
/images/day08/
```

screenshots:

- VNet overview
- Address space
- Subnets page
- Tags page
- CLI validation output

## Cleanup

This lab is low cost, so keep the VNet for Day 9.

To delete:

```bash
az network vnet delete \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-az104-core-uks-001
```

## Official Azure References

- Azure Virtual Network overview: https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview
- Add, change, or delete a subnet: https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-manage-subnet
- Plan virtual networks: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/plan-for-ip-addressing
- Network Security Groups overview: https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview
- Azure Well-Architected Framework: https://learn.microsoft.com/en-us/azure/well-architected/
