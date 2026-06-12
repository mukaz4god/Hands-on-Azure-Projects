# AZ-104 Hands-On Project – Day 13: NAT Gateway + Outbound Connectivity

## Project Overview

This is Day 13 of my 30-day scenario-based AZ-104 Azure Administrator project series.

In this lab, I configured Azure NAT Gateway to provide outbound internet access for a private VM without assigning a public IP address to the VM.

## Scenario

A security team requires that workload VMs should not have public IP addresses. However, those VMs still need outbound internet access for updates, package downloads, and external service calls.

## What I Built

- Azure Virtual Network
- Private subnet
- Network Security Group
- Private Linux VM with no public IP
- Standard Public IP for NAT Gateway
- Azure NAT Gateway
- Subnet association
- Outbound connectivity validation

## Architecture

```text
Private VM
No Public IP
   |
subnet-private
   |
Azure NAT Gateway
   |
Static Public IP
   |
Internet outbound
```

## Skills Practised

- NAT Gateway deployment
- Secure outbound networking
- Private VM design
- Public IP removal strategy
- Subnet-level NAT association
- Run Command validation
- Azure Portal and CLI implementation

## Azure Best Practice Notes

- Do not assign public IPs to private workload VMs.
- Use NAT Gateway for predictable outbound connectivity.
- Use Azure Bastion for secure administration.
- Use Azure Firewall where outbound inspection is required.
- Use NSGs to restrict unnecessary traffic.
- Delete or deallocate lab resources to reduce cost.

## Evidence

Store screenshots in:

```text
/images/day13/
```

Recommended screenshots:

- NAT Gateway overview
- NAT public IP
- Subnet association
- VM with no public IP
- Run Command showing outbound IP
- CLI validation output

## Cleanup

Delete NAT Gateway if finished:

```bash
az network nat gateway delete --resource-group rg-az104-dev-uks-001 --name natgw-uks-001
```

Delete NAT public IP:

```bash
az network public-ip delete --resource-group rg-az104-dev-uks-001 --name pip-nat-uks-001
```

Deallocate VM:

```bash
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-nat-private-01
```

## Official Azure References

- Azure NAT Gateway overview: https://learn.microsoft.com/en-us/azure/nat-gateway/nat-overview
- Quickstart: Create NAT Gateway: https://learn.microsoft.com/en-us/azure/nat-gateway/quickstart-create-nat-gateway-portal
- NAT Gateway resource: https://learn.microsoft.com/en-us/azure/nat-gateway/nat-gateway-resource
- Public IP addresses: https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-addresses
- Azure Well-Architected Framework: https://learn.microsoft.com/en-us/azure/well-architected/
