# AZ-104 Hands-On Project – Day 09: Network Security Groups

## Project Overview

This is Day 09 of my 30-day scenario-based AZ-104 Azure Administrator project series.

In this lab, I secured the Day 8 VNet by applying Network Security Groups to web and app subnets.

## Scenario

A company has created a two-tier Azure network. Security requires that the web subnet only allows HTTP traffic and the app subnet is not directly accessible from the internet.

## What I Built

- Web subnet NSG
- App subnet NSG
- HTTP allow rule
- Internet deny rule
- Subnet-level NSG associations

## Architecture

```text
Internet
   |
   | TCP 80 only
   |
nsg-web-001
   |
subnet-web: 10.0.1.0/24


Internet
   |
   | Denied
   |
nsg-app-001
   |
subnet-app: 10.0.2.0/24
```

## Skills Practised

- NSG creation
- Inbound rule configuration
- Rule priority
- Service tags
- Subnet association
- Least privilege network security
- Azure Portal and Azure CLI validation

## Azure Best Practice Notes

- Allow only required ports.
- Avoid exposing RDP and SSH directly to the internet.
- Apply NSGs at subnet level for consistent control.
- Use clear naming conventions.
- Use service tags where appropriate.
- Validate effective security rules.

## Evidence

Store screenshots in:

```text
/images/day09/
```

Screenshots:

- Web NSG overview
- Web NSG inbound rules
- App NSG overview
- App NSG inbound rules
- Subnet associations
- CLI validation output

## Cleanup

Keep the NSGs for Day 10.

## Official Azure References

- Network Security Groups overview: https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview
- Security rules: https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview#security-rules
- Service tags: https://learn.microsoft.com/en-us/azure/virtual-network/service-tags-overview
- Azure Well-Architected Framework: https://learn.microsoft.com/en-us/azure/well-architected/
