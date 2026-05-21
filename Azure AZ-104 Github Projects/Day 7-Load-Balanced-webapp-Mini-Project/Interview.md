# Day 07 – Interview Questions and Answers

## 1. What is Azure Load Balancer?

Azure Load Balancer is a Layer 4 load balancing service that distributes TCP and UDP traffic across backend resources such as virtual machines.

## 2. What is the difference between public and internal Load Balancer?

A public Load Balancer receives traffic from the internet through a public IP. An internal Load Balancer distributes private traffic inside a virtual network.

## 3. What is a backend pool?

A backend pool is a group of resources, such as VM NICs or IP configurations, that receive traffic from the Load Balancer.

## 4. What is a health probe?

A health probe checks whether backend resources are healthy. If a VM fails the probe, the Load Balancer stops sending traffic to it.

## 5. Why deploy two VMs behind a Load Balancer?

Two VMs reduce single point of failure and allow traffic distribution across more than one backend server.

## 6. What port does HTTP use?

HTTP uses TCP port 80.

## 7. What port does RDP use?

RDP uses TCP port 3389.

## 8. Why should VMs behind a Load Balancer not require public IPs?

Backend VMs should usually remain private. Users should access the application through the Load Balancer frontend public IP.

## 9. How does this project support reliability?

The application can continue serving traffic if one backend VM fails, as long as another healthy VM remains in the backend pool.

## 10. What would you improve for production?

I would add Availability Zones or Availability Sets, use HTTPS with certificates, remove public RDP access, use Azure Bastion, enable monitoring alerts, use VM Scale Sets, and consider Application Gateway or WAF for web-layer protection.

## 11. What is the difference between Load Balancer and Application Gateway?

Azure Load Balancer works at Layer 4 for TCP/UDP traffic. Application Gateway works at Layer 7 and supports HTTP routing, TLS termination, and Web Application Firewall.

## 12. How would you explain this mini project in an interview?

I deployed two Windows Server VMs running IIS behind a public Azure Load Balancer. I configured a frontend public IP, backend pool, health probe, and load balancing rule. I also applied NSG rules to allow HTTP traffic and used tags for governance and cost tracking. This demonstrated resilient VM-based web hosting in Azure.
