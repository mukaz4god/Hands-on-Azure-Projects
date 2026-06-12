# Day 13 – Interview Questions and Answers: NAT Gateway + Outbound Connectivity

## 1. What is Azure NAT Gateway?

Azure NAT Gateway is a managed service that provides outbound internet connectivity for resources in a virtual network subnet.

## 2. Does NAT Gateway allow inbound internet access?

No. NAT Gateway is for outbound connectivity only.

## 3. Where is NAT Gateway associated?

NAT Gateway is associated with a subnet.

## 4. Does a VM need a public IP to use NAT Gateway?

No. A VM can have only a private IP and still access the internet outbound through NAT Gateway.

## 5. What type of public IP does NAT Gateway require?

NAT Gateway requires Standard SKU public IP addresses or public IP prefixes.

## 6. Why is NAT Gateway better than assigning public IPs to every VM?

It reduces direct exposure, centralizes outbound connectivity, and provides predictable egress IP addresses.

## 7. What is the difference between Azure Bastion and NAT Gateway?

Azure Bastion is used for secure inbound administrative access to VMs. NAT Gateway is used for outbound internet connectivity from private resources.

## 8. What is SNAT?

SNAT stands for Source Network Address Translation. It translates the private source IP of a VM to a public IP for outbound internet communication.

## 9. How can you validate NAT Gateway outbound traffic?

Run a command from the VM such as `curl https://ifconfig.me` and confirm the returned IP matches the NAT Gateway public IP.

## 10. What happens if NAT Gateway is removed from the subnet?

Resources in that subnet may lose the NAT Gateway outbound path and fall back to another configured outbound method if one exists.

## 11. When would you use Azure Firewall instead of NAT Gateway?

Use Azure Firewall when you need centralized traffic inspection, filtering, threat intelligence, and application or network rules.

## 12. How would you explain this project in an interview?

I configured Azure NAT Gateway to provide secure outbound internet access for a private VM without assigning a public IP to the VM. I associated NAT Gateway with the subnet, validated outbound connectivity using Run Command, confirmed the outbound IP matched the NAT public IP, and documented the security benefits of removing direct public exposure.
