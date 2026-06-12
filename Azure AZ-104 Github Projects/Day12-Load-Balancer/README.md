# AZ-104 Hands-On Project – Day 12: Azure Load Balancer

## Project Overview

This is Day 12 of my 30-day scenario-based AZ-104 Azure Administrator project series.

In this lab, I deployed a public Azure Standard Load Balancer in front of two Windows Server VMs running IIS.

## Scenario

A company needs a more reliable web application architecture. Instead of relying on one VM, traffic is distributed across two backend web servers using Azure Load Balancer.

## What I Built

- Azure Virtual Network
- Web subnet
- Network Security Group
- Two Windows Server VMs
- IIS web server on both VMs
- Standard Public IP
- Standard Azure Load Balancer
- Frontend IP configuration
- Backend pool
- Health probe
- Load balancing rule

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
vm-lb-web-01           vm-lb-web-02
IIS                    IIS
```

## Skills Practised

- Azure Load Balancer deployment
- Backend pool configuration
- Health probe configuration
- Load balancing rules
- IIS installation using Run Command
- NSG traffic control
- Private backend VM design
- Validation and troubleshooting

## Azure Best Practice Notes

- Use Standard Load Balancer for production.
- Keep backend VMs private.
- Use health probes to remove unhealthy instances.
- Use NSGs to allow only required traffic.
- Use Application Gateway/WAF for Layer 7 web security.
- Use Availability Zones or Availability Sets with backend VMs.
- Deallocate VMs when not in use.

## Evidence

Store screenshots in:

```text
/images/day12/
```

Recommended screenshots:

- Load Balancer overview
- Frontend IP configuration
- Backend pool with two VMs
- Health probe
- Load balancing rule
- NSG HTTP rule
- Browser showing web response
- CLI validation output

## Cleanup

Deallocate VMs:

```bash
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-lb-web-01
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-lb-web-02
```

## Official Azure References

- Azure Load Balancer overview: https://learn.microsoft.com/en-us/azure/load-balancer/load-balancer-overview
- Create a public load balancer: https://learn.microsoft.com/en-us/azure/load-balancer/quickstart-load-balancer-standard-public-portal
- Health probes: https://learn.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview
- Network Security Groups overview: https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview
- Azure Well-Architected Framework: https://learn.microsoft.com/en-us/azure/well-architected/
