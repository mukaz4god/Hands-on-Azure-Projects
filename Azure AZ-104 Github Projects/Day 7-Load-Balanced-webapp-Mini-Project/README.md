# AZ-104 Hands-On Project – Day 07: Load-Balanced Web Application

## Project Overview

This is Day 07 of my 30-day scenario-based AZ-104 Azure Administrator project series.

In this mini project, I deployed two Windows Server web VMs running IIS behind an Azure Load Balancer. I configured NSG rules, applied tags, and validated that web traffic is distributed through a single public IP.

## Scenario

A company needs a resilient test web application hosted on Azure virtual machines. The application must not rely on a single VM, so traffic is distributed across two backend web servers using Azure Load Balancer.

## What I Built

- Virtual Network
- Subnet
- Network Security Group
- Two Windows Server VMs
- IIS on both VMs
- Public Azure Load Balancer
- Backend pool
- Health probe
- Load balancing rule
- Governance tags

## Architecture

```text
Internet
   |
Public IP
   |
Azure Load Balancer
   |
Backend Pool
   |
-------------------------
|                       |
vm-web-01              vm-web-02
IIS                    IIS
```

## Skills Practised

- Azure VM deployment
- IIS web server setup
- Azure Load Balancer configuration
- Backend pools
- Health probes
- NSG inbound rules
- Azure tags
- Validation and troubleshooting

## Azure Best Practice Notes

- Keep backend VMs private where possible.
- Use Load Balancer public IP as the application entry point.
- Allow only required inbound traffic.
- Use tags for ownership and cost tracking.
- Deallocate lab VMs when not in use.
- Use Azure Bastion instead of exposing RDP in production.
- Add Availability Zones or Availability Sets for stronger resilience.

## Evidence

Store screenshots in:

```text
/images/day07/
```

Recommended screenshots:

- VNet and subnet
- NSG inbound rules
- Two VMs running
- Load Balancer overview
- Backend pool with both VMs
- Health probe
- Browser showing web page
- Tags on resources

## Cleanup

```bash
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-web-01
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-web-02
```

## Official Azure References

- Azure Load Balancer overview: https://learn.microsoft.com/en-us/azure/load-balancer/load-balancer-overview
- Quickstart: Create a public Load Balancer: https://learn.microsoft.com/en-us/azure/load-balancer/quickstart-load-balancer-standard-public-portal
- Network Security Groups overview: https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview
- Azure Virtual Machines overview: https://learn.microsoft.com/en-us/azure/virtual-machines/overview
- Use tags to organize Azure resources: https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/tag-resources
- Azure Well-Architected Framework: https://learn.microsoft.com/en-us/azure/well-architected/
