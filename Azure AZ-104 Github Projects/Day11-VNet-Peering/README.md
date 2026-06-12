# AZ-104 Hands-On Project – Day 11: VNet Peering

## Project Overview

This is Day 11 of my 30-day scenario-based AZ-104 Azure Administrator project series.

In this lab, I created two separate Azure Virtual Networks and configured bidirectional VNet peering to enable private connectivity between them.

## Scenario

An organisation has separate application networks for web and backend services. The teams need private communication between the networks without exposing services to the public internet.

## What I Built

- Web VNet
- App VNet
- Web subnet
- App subnet
- Bidirectional VNet peering
- Optional test VMs
- Private connectivity validation

## Architecture

```text
vnet-web-uks-001
10.11.0.0/16
   |
   | peer-web-to-app
   |
vnet-app-uks-001
10.12.0.0/16
```

## Skills Practised

- Azure VNet creation
- Subnet planning
- VNet peering
- Private IP connectivity
- NSG-aware troubleshooting
- Enterprise network segmentation
- Hub-and-spoke architecture awareness

## Azure Best Practice Notes

- Use non-overlapping IP ranges.
- Document address plans early.
- Remember VNet peering is not transitive.
- Use NSGs to control traffic even across peering.
- Consider Azure Firewall or hub-and-spoke architecture for enterprise networks.
- Use Private DNS for name resolution across VNets.

## Evidence

Store screenshots in:

```text
/images/day11/
```

Recommended screenshots:

- Web VNet overview
- App VNet overview
- Peering status from Web to App
- Peering status from App to Web
- Optional connectivity test output
- CLI validation output

## Cleanup

Deallocate test VMs if used:

```bash
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-web-peer-01
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-app-peer-01
```

## Official Azure References

- VNet peering overview: https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview
- Create VNet peering: https://learn.microsoft.com/en-us/azure/virtual-network/tutorial-connect-virtual-networks-portal
- Azure Virtual Network overview: https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview
- Azure Well-Architected Framework: https://learn.microsoft.com/en-us/azure/well-architected/
