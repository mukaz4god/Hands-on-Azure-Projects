# AZ-104 Hands-On Project – Day 10: Private vs Public IP and Azure Bastion

## Project Overview

This is Day 10 of my 30-day scenario-based AZ-104 Azure Administrator project series.

In this lab, I secured virtual machine administration by removing direct public IP exposure and using Azure Bastion for private RDP/SSH access.

## Scenario

A security review found that public IPs and management ports can expose VMs to unnecessary risk. The goal was to use Azure Bastion so administrators can access VMs securely without public IPs on the target VMs.

## What I Built

- `AzureBastionSubnet`
- Azure Bastion host
- Bastion public IP
- Private VM access model
- Public IP removal workflow

## Architecture

```text
Administrator Browser
   |
Azure Portal
   |
Azure Bastion
   |
Private IP
   |
Azure VM
```

## Skills Practised

- Public vs private IP understanding
- Azure Bastion deployment
- Secure VM administration
- NIC public IP removal
- Private access validation
- Azure Portal connectivity

## Azure Best Practice Notes

- Avoid direct RDP/SSH exposure to internet.
- Use Bastion, VPN, or private connectivity.
- Remove public IPs from workload VMs where possible.
- Use NSGs to control management traffic.
- Delete Bastion after lab if cost is a concern.

## Evidence

Store screenshots in:

```text
/images/day10/
```

Recommended screenshots:

- `AzureBastionSubnet`
- Bastion resource overview
- VM with no public IP
- Bastion connection page
- Successful Bastion session
- CLI validation output

## Cleanup

Azure Bastion may incur cost.

Delete Bastion after the lab if you do not need it:

```bash
az network bastion delete \
  --resource-group rg-az104-dev-uks-001 \
  --name bas-az104-uks-001
```

Delete Bastion public IP:

```bash
az network public-ip delete \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-bastion-uks-001
```

## Official Azure References

- Azure Bastion overview: https://learn.microsoft.com/en-us/azure/bastion/bastion-overview
- Create Azure Bastion: https://learn.microsoft.com/en-us/azure/bastion/quickstart-host-portal
- Public IP addresses in Azure: https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-addresses
- Private IP addresses in Azure: https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/private-ip-addresses
- Azure Well-Architected Framework: https://learn.microsoft.com/en-us/azure/well-architected/
