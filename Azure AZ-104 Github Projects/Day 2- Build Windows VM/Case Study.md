# Day 02 – Azure Compute Lab: Deploying a Windows Virtual Machine

## Scenario

Your organisation is preparing its first cloud-hosted application. As the Azure Administrator, you are tasked with deploying a Windows Server virtual machine that will later host a web application.

This VM must be securely deployed inside a virtual network, protected by a network security group, and accessible remotely via RDP.

## Business Requirement

Deploy a Windows Server VM that:
- Resides in a secure virtual network
- Is accessible for administration via RDP (port 3389)
- Can be extended in future labs

## Technical Requirements

- Create a Virtual Network (VNet)
- Create a Subnet
- Create a Network Security Group (NSG)
- Allow inbound RDP (TCP 3389)
- Create a Windows Virtual Machine
- Attach:
  - Network Interface
  - Public IP
  - NSG

## AZ-104 Skills Covered

- Deploy Azure Virtual Machines
- Configure virtual networking
- Configure NSGs
- Configure public and private IP addressing
- Understand VM architecture (NIC, subnet, NSG, disk)

## Azure Well-Architected Alignment

| Pillar | Implementation |
|--------|--------------|
| Security | NSG restricts access to RDP only |
| Reliability | VM deployed in controlled network |
| Cost | Basic SKU used (can optimize later) |
| Ops | Structured deployment using RG |
| Performance | Right VM size selection |

## Success Criteria

- VM is deployed and running
- RDP connection works
- NSG allows only port 3389 inbound
- VM is inside correct subnet
