# Day 02 – Deploy Windows Virtual Machine (AZ-104)

## Overview

This lab demonstrates how to deploy a Windows VM with full networking components.

## What was built

- Virtual Network
- Subnet
- NSG (RDP allowed)
- Public IP
- Network Interface
- Windows VM

## Architecture

```
Internet
   |
Public IP
   |
NSG (Allow 3389)
   |
NIC
   |
Subnet (10.0.1.0/24)
   |
VNet (10.0.0.0/16)
```

## Skills

- VM deployment
- Networking
- Security (NSG)

## Azure Best Practice

- Always restrict RDP access
- Use NSGs at subnet or NIC level
- Avoid exposing unnecessary ports

## Next Step

Day 3 – Connect + Install Web Server
