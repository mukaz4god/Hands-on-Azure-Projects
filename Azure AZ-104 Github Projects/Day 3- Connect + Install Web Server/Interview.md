# Day 03 – Interview Questions and Answers

## 1. How do you connect to a Windows VM in Azure?

You connect using RDP over TCP port 3389. The VM needs a public IP or private connectivity through VPN, ExpressRoute, Azure Bastion, or a jump box.

## 2. Is exposing RDP to the internet recommended?

No. It is acceptable for a controlled learning lab, but in production RDP should not be exposed directly to the internet. Better options include Azure Bastion, VPN, Just-in-Time VM access, or private connectivity.

## 3. What is IIS?

IIS, Internet Information Services, is Microsoft’s web server role for hosting websites and web applications on Windows Server.

## 4. What port does HTTP use by default?

HTTP uses TCP port 80 by default.

## 5. What port does HTTPS use by default?

HTTPS uses TCP port 443 by default.

## 6. What is the role of an NSG in this lab?

The NSG controls inbound and outbound network traffic to the VM. In this lab, it allows RDP on port 3389 for administration and HTTP on port 80 for web access.

## 7. Why do we need both RDP and HTTP rules?

RDP is needed to administer the Windows VM. HTTP is needed so users can access the IIS website from a browser.

## 8. What happens if IIS is installed but HTTP is blocked in the NSG?

The website may work locally inside the VM, but external users will not be able to reach it because Azure network traffic is blocked before it reaches the VM.

## 9. What is the difference between Windows Firewall and NSG?

Windows Firewall controls traffic inside the operating system. NSG controls traffic at the Azure network level, either at subnet or NIC scope. Both can affect access.

## 10. How are NSG rules processed?

NSG rules are processed by priority. Lower numbers have higher priority, and the first matching rule is applied.

## 11. What would you improve for production?

For production, I would remove direct RDP exposure, use Azure Bastion or VPN, restrict source IP addresses, enable monitoring, configure HTTPS, and place the web server behind a load balancer or application gateway.

## 12. How would you explain this lab in an interview?

I deployed and configured a Windows VM as a web server in Azure. I connected via RDP, installed IIS, created a test webpage, updated the NSG to allow HTTP traffic, and validated access through the VM public IP. I also considered security risks around exposing RDP and documented production improvements.
