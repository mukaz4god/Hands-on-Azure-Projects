# Day 12 – Enterprise Compute Lab: Azure Load Balancer

## Scenario

Your organisation is hosting a web application on Azure virtual machines. Currently, the application depends on a single server, which creates a single point of failure.

The business requires the application to remain available if one virtual machine becomes unavailable. To meet this requirement, you must deploy two web servers behind an Azure Standard Load Balancer.

As the Azure Administrator, your task is to design and deploy a load-balanced web application using Azure virtual machines, backend pools, health probes, and load balancing rules.

---

## Business Requirements

The solution must:

- Host a basic web application on two VMs.
- Present a single public IP address to users.
- Distribute HTTP traffic across both VMs.
- Detect unhealthy backend VMs using a health probe.
- Avoid exposing unnecessary ports.
- Use clear naming conventions and tags.
- Follow Azure reliability and security best practices.

---

## Technical Requirements

Create:

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| VNet | `vnet-lb-uks-001` |
| Subnet | `subnet-web` |
| NSG | `nsg-lb-web-001` |
| VM 1 | `vm-lb-web-01` |
| VM 2 | `vm-lb-web-02` |
| Public IP | `pip-lb-uks-001` |
| Load Balancer | `lb-web-uks-001` |
| Frontend IP config | `feip-web-001` |
| Backend pool | `bepool-web-001` |
| Health probe | `probe-http-80` |
| LB rule | `rule-http-80` |

---

## Architecture

```text
Internet
   |
   | HTTP TCP/80
   |
Public IP: pip-lb-uks-001
   |
Azure Standard Load Balancer: lb-web-uks-001
   |
Backend Pool: bepool-web-001
   |
-------------------------------
|                             |
vm-lb-web-01                 vm-lb-web-02
IIS Web Server               IIS Web Server
subnet-web                   subnet-web
```

---

## Why This Design Matters

A single VM web application has limited availability. If the VM fails, the application becomes unavailable.

Using Azure Load Balancer improves reliability by:

- Distributing traffic across multiple backend VMs.
- Detecting unhealthy VMs using health probes.
- Removing failed instances from rotation.
- Providing one stable public frontend IP.

---

## Enterprise Considerations

In enterprise environments, Azure Load Balancer is commonly used for:

- Highly available web tiers
- Internal application tiers
- Database listener scenarios
- Private backend services
- TCP/UDP traffic distribution

For HTTP-specific routing, TLS termination, host-based routing, path-based routing, or Web Application Firewall, Azure Application Gateway may be more appropriate.

---

## AZ-104 Skills Covered

- Deploy Azure Load Balancer.
- Configure public frontend IP.
- Configure backend pools.
- Configure health probes.
- Configure load balancing rules.
- Deploy and configure backend VMs.
- Install IIS using Run Command.
- Validate traffic distribution.
- Understand Standard vs Basic Load Balancer.
- Understand Layer 4 load balancing.

---

## Azure Well-Architected Framework Alignment

| Pillar | How this lab supports it |
|---|---|
| Reliability | Two backend VMs reduce dependency on a single server. |
| Security | NSG allows only required traffic. |
| Operational Excellence | Health probes support automated backend health detection. |
| Cost Optimization | Small VM sizes are used for lab cost control. |
| Performance Efficiency | Load balancing distributes traffic across backend instances. |

---

## Success Criteria

You should be able to demonstrate:

- Two backend VMs running IIS.
- Load Balancer frontend public IP exists.
- Backend pool contains both VM NICs.
- Health probe is configured on TCP 80.
- Load balancing rule maps frontend TCP 80 to backend TCP 80.
- Website is reachable using the Load Balancer public IP.
- Refreshing the page can show responses from both VMs.
