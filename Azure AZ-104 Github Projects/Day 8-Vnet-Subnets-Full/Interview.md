# Day 08 – Interview Questions and Answers

## 1. What is an Azure Virtual Network?

An Azure Virtual Network is a private, isolated network in Azure. It allows Azure resources such as virtual machines to communicate securely with each other, the internet, and on-premises networks.

## 2. What is a subnet?

A subnet is a smaller address range inside a Virtual Network. Subnets are used to organise and isolate resources.

## 3. Why do we create separate web and app subnets?

Separate subnets allow better security and traffic control. For example, web servers can receive inbound traffic, while application servers can remain private and only accept traffic from the web tier.

## 4. What does `10.0.0.0/16` mean?

It is a CIDR address range. `/16` means the first 16 bits represent the network portion, giving a large private IP range from `10.0.0.0` to `10.0.255.255`.

## 5. What does `10.0.1.0/24` mean?

It is a smaller subnet inside the VNet. `/24` gives 256 addresses, but Azure reserves 5, leaving 251 usable IP addresses.

## 6. How many IP addresses does Azure reserve in every subnet?

Azure reserves 5 IP addresses in every subnet.

## 7. Can subnet ranges overlap?

No. Subnet ranges inside the same VNet cannot overlap.

## 8. Why should VNet address spaces not overlap?

Overlapping address spaces cause routing problems, especially when using VNet peering, VPN, or ExpressRoute.

## 9. What is the relationship between VNet and NSG?

A VNet provides the network boundary. An NSG controls inbound and outbound traffic and can be applied to a subnet or network interface.

## 10. Can a VM be deployed into a VNet in a different region?

No. A VM and the VNet it is connected to must be in the same region.

## 11. How does this lab support Azure Well-Architected best practices?

It supports security through subnet segmentation, reliability through proper IP planning, and operational excellence through clear naming and documentation.

## 12. How would you explain this lab in an interview?

I designed and deployed an Azure Virtual Network using a multi-tier subnet model. I created a `/16` VNet and two `/24` subnets for web and application tiers. This prepares the environment for future NSG rules, workload isolation, and scalable cloud architecture.
