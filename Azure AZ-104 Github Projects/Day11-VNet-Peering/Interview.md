# Day 11 – Interview Questions and Answers: VNet Peering

## 1. What is VNet peering?

VNet peering connects two Azure virtual networks so resources can communicate privately using Azure backbone networking.

## 2. Does VNet peering use the public internet?

No. Traffic flows through the Microsoft backbone network.

## 3. Can VNet address spaces overlap?

No. Peered VNets must have non-overlapping address spaces.

## 4. Is VNet peering transitive?

No. If VNet A is peered with VNet B and VNet B is peered with VNet C, VNet A cannot automatically communicate with VNet C.

## 5. What is regional VNet peering?

Regional peering connects VNets in the same Azure region.

## 6. What is global VNet peering?

Global peering connects VNets across different Azure regions.

## 7. Can NSGs still filter traffic across peered VNets?

Yes. NSGs still apply to subnet and NIC traffic even when VNets are peered.

## 8. What happens if only one peering direction is configured?

The peering may show as initiated and bidirectional connectivity will not work correctly. You should configure both directions.

## 9. What is a hub-and-spoke network?

A hub-and-spoke network uses a central hub VNet for shared services such as firewall, VPN, DNS, or monitoring, while spoke VNets host workloads.

## 10. When would you use VNet peering instead of VPN Gateway?

Use peering for private Azure-to-Azure VNet connectivity. Use VPN Gateway for encrypted connectivity to on-premises or external networks.

## 11. Does VNet peering encrypt traffic?

Traffic remains on the Microsoft backbone. For additional encryption requirements, application-level encryption or VPN may be used depending on compliance needs.

## 12. How would you explain this project in an interview?

I created two non-overlapping Azure VNets, configured bidirectional VNet peering, validated the peering status, and tested private connectivity between workloads. I also documented key enterprise considerations such as non-transitive routing, NSG filtering, and hub-and-spoke design.
