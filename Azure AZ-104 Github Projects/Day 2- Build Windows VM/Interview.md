# Day 02 – Interview Questions

## What components are required to deploy a VM in Azure?

- Virtual Network
- Subnet
- Network Interface
- NSG
- Public IP (optional)
- OS Disk

## What is an NSG?

A Network Security Group filters inbound and outbound traffic using rules based on IP, port, and protocol.

## Why restrict RDP access?

To reduce attack surface and follow least privilege.

## What is the difference between Public IP and Private IP?

Public IP allows internet access, private IP is internal within the VNet.

## Explain VM networking architecture

A VM connects via a NIC to a subnet inside a VNet, optionally with NSG and public IP.
