# Day 10 – Interview Questions and Answers

## 1. What is the difference between a public IP and private IP in Azure?

A public IP allows a resource to be reachable from the internet. A private IP is used for communication inside a VNet or connected private network.

## 2. Why should VMs not expose RDP or SSH directly to the internet?

Because exposed management ports are common attack targets. They increase the risk of brute force attacks, exploitation, and unauthorized access.

## 3. What is Azure Bastion?

Azure Bastion is a managed service that provides secure RDP and SSH access to VMs through the Azure Portal without requiring public IPs on the VMs.

## 4. What subnet is required for Azure Bastion?

Azure Bastion requires a subnet named exactly `AzureBastionSubnet`.

## 5. What size should the Bastion subnet be?

A `/26` or larger subnet is recommended.

## 6. Does the target VM need a public IP when using Bastion?

No. The target VM only needs a private IP in the VNet.

## 7. What is a jump box?

A jump box is a hardened VM used as an administrative entry point to access private resources.

## 8. Bastion vs Jump Box: which is preferred?

Azure Bastion is often preferred because it is managed by Azure, integrates with the Portal, and avoids exposing management ports directly.

## 9. What happens after removing a VM public IP?

The VM is no longer directly reachable from the internet, but it can still communicate privately within the VNet and be accessed through Bastion or private connectivity.

## 10. How does this lab support security best practice?

It reduces attack surface by removing public management access and using a controlled access path through Bastion.

## 11. What would you recommend in production?

Use Bastion or VPN, remove public IPs from workloads, enforce NSGs, enable monitoring, use MFA, and apply least privilege RBAC.

## 12. How would you explain this project in an interview?

I secured VM administration by removing direct public IP exposure and deploying Azure Bastion. I created the required Bastion subnet, deployed Bastion, validated private VM access, and confirmed that the VM no longer required direct public RDP or SSH access.
