# Day 09 – Interview Questions and Answers

## 1. What is a Network Security Group?

A Network Security Group is an Azure network filtering service that controls inbound and outbound traffic using security rules.

## 2. At what layer does an NSG operate?

An NSG mainly operates at Layer 3 and Layer 4, filtering based on source, destination, port, and protocol.

## 3. Are NSGs stateful?

Yes. If inbound traffic is allowed, the return outbound traffic is automatically allowed.

## 4. Where can you associate an NSG?

An NSG can be associated with a subnet or a network interface.

## 5. What happens if an NSG is applied at both subnet and NIC level?

Both NSGs are evaluated. Traffic must be allowed by both effective rule sets.

## 6. How does NSG priority work?

Lower priority numbers are evaluated first. A rule with priority 100 is processed before a rule with priority 200.

## 7. What is the default inbound behaviour of an NSG?

By default, inbound traffic from the internet is denied unless an allow rule is created.

## 8. Why allow only HTTP to the web subnet?

It follows least privilege. The web tier only receives the traffic required for its function.

## 9. Why should the app subnet not be internet accessible?

The app tier should be internal and only accept traffic from trusted tiers, such as the web subnet.

## 10. What is the difference between NSG and Azure Firewall?

NSGs provide basic subnet/NIC traffic filtering. Azure Firewall is a centralized managed firewall with advanced capabilities such as threat intelligence and application-level rules.

## 11. How does this relate to AppSec?

Misconfigured cloud network rules can expose applications and management ports. NSGs reduce attack surface and support defence in depth.

## 12. How would you explain this project in an interview?

I secured a two-tier Azure network by creating subnet-level NSGs. I allowed only HTTP traffic to the web subnet and blocked direct internet inbound traffic to the app subnet. I validated subnet associations and rule priorities using Azure CLI and Portal, applying least privilege networking principles.
