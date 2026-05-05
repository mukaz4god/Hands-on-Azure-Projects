# Day 03 – Azure Compute Lab: Connect to Windows VM and Install IIS Web Server

## Scenario

Your organisation has successfully deployed its first Windows Server virtual machine in Azure. The next task is to configure the VM as a basic web server so the application team can later deploy a simple internal or public-facing website.

As the Azure Administrator, you must securely connect to the VM using RDP, install the IIS web server role, validate HTTP access, and update the network security configuration to allow web traffic.

## Business Requirement

The business needs a Windows Server VM configured as a web server and reachable over HTTP for testing.

## Technical Requirements

- Connect to the Windows VM using RDP.
- Install IIS Web Server.
- Create a simple test webpage.
- Configure NSG to allow inbound HTTP traffic on TCP port 80.
- Validate access from a browser using the VM public IP.
- Capture screenshots for GitHub evidence.

## Assumptions

This lab assumes Day 2 has already been completed and the following resources exist:

- Resource Group: `rg-az104-dev-uks-001`
- VM: `vm-win-001`
- VNet: `vnet-az104-dev-001`
- Subnet: `subnet-web`
- NSG: `nsg-rdp-001`
- Public IP attached to the VM NIC

## AZ-104 Skills Covered

- Connect to Azure virtual machines
- Configure VM operating system services
- Configure Network Security Group rules
- Validate VM connectivity
- Troubleshoot access to Azure-hosted workloads

## Azure Well-Architected Framework Alignment

| Pillar | How this lab supports it |
|---|---|
| Security | Only required inbound ports are opened: RDP for admin and HTTP for web testing. |
| Reliability | IIS service validation confirms the workload is running. |
| Operational Excellence | Configuration is documented and repeatable. |
| Cost Optimization | Reuses existing Day 2 VM instead of deploying unnecessary resources. |
| Performance Efficiency | Basic web server deployment provides foundation for future scaling and load balancing labs. |

## Success Criteria

You should be able to show:

- Successful RDP connection to the VM.
- IIS installed and running.
- NSG rule allowing TCP 80.
- Browser access to the IIS test page using the VM public IP.
- Screenshots stored for portfolio evidence.

## Real-World Security Note

Opening RDP directly to the internet is not recommended for production. In real environments, use Azure Bastion, Just-in-Time VM access, VPN, or a hardened jump box. This lab allows RDP only for learning purposes.
