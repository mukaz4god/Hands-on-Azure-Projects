# Day 07 – Mini Project: Load-Balanced Web Application on Azure VMs

## Scenario

Your organisation wants to host a simple highly available web application in Azure using Infrastructure-as-a-Service. The application team has requested two web servers so that traffic is not dependent on a single virtual machine.

As the Azure Administrator, your job is to deploy two Windows Server web VMs, install IIS on both, place them behind an Azure Load Balancer, secure access with Network Security Groups, and apply consistent tags for governance and cost tracking.

This mini project brings together the key concepts from Days 1–6: resource groups, tags, virtual networks, subnets, NSGs, virtual machines, IIS, availability design, and load balancing.

## Business Requirements

The business needs a resilient test web application that:

- Runs on two backend virtual machines.
- Is accessible through a single public frontend IP.
- Uses a Load Balancer to distribute traffic.
- Allows HTTP access on port 80.
- Allows controlled RDP access for administration.
- Uses tags for ownership, environment, project, and cost tracking.

## Technical Requirements

You will deploy:

- 1 virtual network.
- 1 subnet.
- 1 Network Security Group.
- 2 Windows Server VMs.
- IIS installed on both VMs.
- 1 public Azure Load Balancer.
- 1 backend pool.
- 1 health probe.
- 1 load balancing rule.
- Required tags.

## Architecture

```text
Internet
   |
   | HTTP :80
   |
Public IP Address
   |
Azure Load Balancer
   |
Backend Pool
   |
--------------------------------
|                              |
VM 1: vm-web-01               VM 2: vm-web-02
IIS Web Server                IIS Web Server
Subnet: subnet-web            Subnet: subnet-web
VNet: vnet-lb-web-uks-001     VNet: vnet-lb-web-uks-001
```

## AZ-104 Skills Covered

- Deploy and manage Azure virtual machines.
- Configure virtual networks and subnets.
- Configure Network Security Groups.
- Configure Azure Load Balancer.
- Configure health probes and backend pools.
- Apply tags to Azure resources.
- Validate web application availability.
- Understand basic high availability architecture.

## Azure Well-Architected Framework Alignment

| Pillar | How this project supports it |
|---|---|
| Reliability | Two backend VMs reduce dependency on a single VM. |
| Security | NSG controls inbound traffic to only required ports. |
| Cost Optimization | Tags support cost tracking; small VM sizes reduce lab cost. |
| Operational Excellence | Clear naming, validation, and documentation improve supportability. |
| Performance Efficiency | Load Balancer distributes traffic across backend VMs. |

## Success Criteria

You should be able to demonstrate:

- Two VMs are running IIS.
- Load Balancer frontend IP returns the web page.
- Each VM has a different webpage identifier.
- Backend pool contains both VMs.
- Health probe reports healthy backend instances.
- NSG allows HTTP and controlled RDP only.
- Tags are applied to key resources.

## Professional Note

In production, direct RDP exposure should be avoided. Use Azure Bastion, VPN, Just-in-Time VM access, or privileged access workstations. This lab keeps RDP open only for learning and validation.
