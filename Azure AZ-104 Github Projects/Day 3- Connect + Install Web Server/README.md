# AZ-104 Hands-On Project – Day 03: Connect to Windows VM and Install IIS

## Project Overview

This is Day 03 of my 30-day scenario-based AZ-104 Azure Administrator hands-on project series.

In this lab, I connected to the Windows Server VM created in Day 2, installed IIS, configured an HTTP NSG rule, and validated browser access to the hosted webpage.

## Scenario

A company has deployed its first Windows Server VM in Azure and now wants to configure it as a basic web server for application testing.

## What I Built

- RDP connection to Azure Windows VM
- IIS Web Server installation
- Custom test webpage
- NSG inbound rule for HTTP port 80
- Browser-based validation using public IP

## Technologies Used

- Microsoft Azure
- Azure Virtual Machines
- Azure Virtual Network
- Network Security Group
- Public IP Address
- Windows Server
- IIS
- Azure CLI
- PowerShell

## Architecture

```text
User Browser / RDP Client
        |
        |  RDP 3389 / HTTP 80
        |
    Public IP
        |
    NSG
    - Allow-RDP 3389
    - Allow-HTTP 80
        |
    NIC
        |
    Windows VM
    - IIS Web Server
        |
    Subnet: subnet-web
        |
    VNet: vnet-az104-dev-001
```

## Files in This Folder

| File | Purpose |
|---|---|
| `Case Study.md` | Scenario, requirements, AZ-104 mapping |
| `Setup Guide.md` | Step-by-step implementation guide |
| `Interview.md` | Interview questions and answers |
| `README.md` | GitHub project overview |

## Key Learning Outcomes

By completing this lab, I practised:

- Connecting to Azure Windows VMs using RDP
- Installing and validating IIS
- Configuring NSG inbound rules
- Testing public access to Azure-hosted workloads
- Understanding the difference between OS firewall and Azure NSG controls

## Azure Best Practice Notes

- Avoid exposing RDP directly to the internet in production.
- Restrict RDP source IPs where possible.
- Use Azure Bastion, VPN, or Just-in-Time access for secure administration.
- Open only the ports required by the workload.
- Use NSG rule priority carefully because lower numbers are evaluated first.
- Use HTTPS instead of HTTP for production workloads.

## Evidence

Add screenshots to:

```text
/images/day03/
```

Recommended screenshots:

- RDP session connected to VM
- IIS installed successfully
- Browser showing custom IIS webpage
- NSG inbound rules
- CLI validation output

## Cleanup / Cost Control

To stop compute billing after the lab:

```bash
az vm deallocate \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-win-001
```

## Official Azure References

- Connect to Windows VM using RDP: https://learn.microsoft.com/en-us/azure/virtual-machines/windows/connect-rdp
- Create a Windows VM in Azure: https://learn.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal
- Network security groups: https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview
- Open ports to a VM: https://learn.microsoft.com/en-us/azure/virtual-machines/windows/nsg-quickstart-portal
- Azure Bastion overview: https://learn.microsoft.com/en-us/azure/bastion/bastion-overview
- Azure Well-Architected Framework: https://learn.microsoft.com/en-us/azure/well-architected/
