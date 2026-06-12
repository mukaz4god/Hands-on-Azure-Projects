# Day 12 – Interview Questions and Answers: Azure Load Balancer

## 1. What is Azure Load Balancer?

Azure Load Balancer is a Layer 4 load balancing service that distributes TCP and UDP traffic across backend resources.

## 2. What OSI layer does Azure Load Balancer operate at?

Azure Load Balancer operates at Layer 4, the transport layer.

## 3. What is a frontend IP configuration?

A frontend IP configuration is the IP address clients connect to. It can be public or private.

## 4. What is a backend pool?

A backend pool is a group of backend resources, usually VM NICs or IP configurations, that receive traffic from the load balancer.

## 5. What is a health probe?

A health probe checks whether backend instances are healthy. If a backend instance fails the probe, the Load Balancer stops sending traffic to it.

## 6. What is a load balancing rule?

A load balancing rule maps frontend traffic to backend pool traffic. For example, frontend TCP 80 to backend TCP 80.

## 7. What is the difference between public and internal Load Balancer?

A public Load Balancer receives traffic from the internet. An internal Load Balancer distributes traffic privately inside a VNet.

## 8. Why use Standard Load Balancer?

Standard Load Balancer is recommended for production because it supports better scale, availability zones, secure-by-default behaviour, and more advanced features.

## 9. Why should backend VMs not have public IPs?

Backend VMs should stay private to reduce attack surface. Users should connect through the Load Balancer frontend IP.

## 10. What is the difference between Azure Load Balancer and Application Gateway?

Azure Load Balancer is Layer 4 and handles TCP/UDP. Application Gateway is Layer 7 and supports HTTP routing, TLS termination, cookie affinity, and Web Application Firewall.

## 11. What happens when a health probe fails?

The Load Balancer stops sending new traffic flows to that backend instance until it becomes healthy again.

## 12. How would you explain this project in an interview?

I deployed a Standard Azure Load Balancer with two backend Windows VMs running IIS. I configured a public frontend IP, backend pool, TCP health probe, and load balancing rule for HTTP traffic. I validated the application through the Load Balancer public IP and documented security and production improvements.
