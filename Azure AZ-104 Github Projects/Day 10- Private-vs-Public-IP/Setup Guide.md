# Day 10 – Setup Guide: Private vs Public IP and Azure Bastion

## Objective

Securely access Azure VMs using Azure Bastion instead of direct public IP access.

This guide includes:

- Azure CLI method
- Azure Portal method
- Public IP removal guidance
- Bastion connection steps
- Validation and troubleshooting
- Cost control

## Existing Resources

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| VNet | `vnet-az104-core-uks-001` |
| Web Subnet | `subnet-web` |
| App Subnet | `subnet-app` |

## New Resources

| Resource | Name |
|---|---|
| Bastion Subnet | `AzureBastionSubnet` |
| Bastion Public IP | `pip-bastion-uks-001` |
| Bastion Host | `bas-az104-uks-001` |

Important:

Azure Bastion requires a subnet named exactly:

```text
AzureBastionSubnet
```

The recommended minimum subnet size is `/26`.

---

# Method 1 – Azure CLI

## Step 1 – Confirm VNet Exists

```bash
az network vnet show \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-az104-core-uks-001 \
  --output table
```

## Step 2 – Create AzureBastionSubnet

```bash
az network vnet subnet create \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-az104-core-uks-001 \
  --name AzureBastionSubnet \
  --address-prefixes 10.0.10.0/26
```

Why `/26`?

Azure Bastion requires enough IP addresses for scale and platform operations.

## Step 3 – Create Bastion Public IP

```bash
az network public-ip create \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-bastion-uks-001 \
  --location uksouth \
  --sku Standard \
  --allocation-method Static \
  --tags env=dev owner="<your-name>" project=az104-day10-private-access costCenter=learning
```

## Step 4 – Create Azure Bastion Host

```bash
az network bastion create \
  --resource-group rg-az104-dev-uks-001 \
  --name bas-az104-uks-001 \
  --public-ip-address pip-bastion-uks-001 \
  --vnet-name vnet-az104-core-uks-001 \
  --location uksouth
```

This can take several minutes.

## Step 5 – Identify VM Public IPs

List public IPs:

```bash
az network public-ip list \
  --resource-group rg-az104-dev-uks-001 \
  --query "[].{Name:name, IP:ipAddress}" \
  --output table
```

## Step 6 – Remove Public IP from a VM NIC

First, identify the VM NIC:

```bash
az vm show \
  --resource-group rg-az104-dev-uks-001 \
  --name <vm-name> \
  --query "networkProfile.networkInterfaces[0].id" \
  --output tsv
```

List NICs:

```bash
az network nic list \
  --resource-group rg-az104-dev-uks-001 \
  --query "[].name" \
  --output table
```

Show IP configuration:

```bash
az network nic ip-config list \
  --resource-group rg-az104-dev-uks-001 \
  --nic-name <nic-name> \
  --output table
```

Remove public IP from NIC:

```bash
az network nic ip-config update \
  --resource-group rg-az104-dev-uks-001 \
  --nic-name <nic-name> \
  --name <ipconfig-name> \
  --remove publicIPAddress
```

## Step 7 – Validate VM Still Has Private IP

```bash
az network nic ip-config list \
  --resource-group rg-az104-dev-uks-001 \
  --nic-name <nic-name> \
  --query "[].{Name:name, PrivateIP:privateIPAddress, PublicIP:publicIPAddress.id}" \
  --output table
```

Expected:

```text
PrivateIP exists
PublicIP is empty
```

## Step 8 – Connect Using Bastion

Azure CLI Bastion connection requires extensions and local client support. For this lab, use the Azure Portal for the actual Bastion session because it is simpler and AZ-104-friendly.

---

# Method 2 – Azure Portal

## Step 1 – Create AzureBastionSubnet

1. Open Azure Portal.
2. Search for **Virtual networks**.
3. Open `vnet-az104-core-uks-001`.
4. Go to **Subnets**.
5. Click **+ Subnet**.
6. Configure:
   - Name: `AzureBastionSubnet`
   - Starting address: `10.0.10.0`
   - Size: `/26`
   - Address range: `10.0.10.0/26`
7. Click **Save**.

Important:

The subnet name must be exactly `AzureBastionSubnet`.

## Step 2 – Create Azure Bastion

1. Search for **Bastions**.
2. Click **Create**.
3. Configure:
   - Resource Group: `rg-az104-dev-uks-001`
   - Name: `bas-az104-uks-001`
   - Region: `UK South`
   - Tier/SKU: Basic or Developer if available for lab, otherwise Standard
   - Virtual Network: `vnet-az104-core-uks-001`
   - Subnet: `AzureBastionSubnet`
   - Public IP: Create new
   - Public IP name: `pip-bastion-uks-001`
4. Click **Review + create**.
5. Click **Create**.

## Step 3 – Remove Public IP from a VM

1. Search for **Virtual machines**.
2. Open the target VM.
3. Go to **Networking**.
4. Click the VM network interface.
5. Go to **IP configurations**.
6. Select the IP configuration.
7. Under **Public IP address**, choose **Dissociate** or set to **None**.
8. Save.

Validation:

The VM should still have a private IP address but no public IP address.

## Step 4 – Connect to VM Using Bastion

1. Open the VM in Azure Portal.
2. Click **Connect**.
3. Select **Bastion**.
4. Enter credentials:
   - Windows VM: username and password
   - Linux VM: username and SSH key/password depending on setup
5. Click **Connect**.

The session opens in the browser.

## Step 5 – Validate No Direct Public Access

Check the VM overview page:

- Public IP address should be empty.
- Private IP address should still exist.

Try connecting directly from your local machine using RDP or SSH. It should fail because there is no public IP.

---

# Optional Jump Box Alternative

A jump box is a VM with controlled access that administrators use to reach private VMs.

However, for this AZ-104 project, Azure Bastion is preferred because:

- No public IP is required on target VMs.
- RDP/SSH is accessed through the portal.
- No client agent is needed.
- It is a Microsoft-managed service.

---

# Validation Checklist

| Check | Expected Result |
|---|---|
| Bastion subnet exists | `AzureBastionSubnet` |
| Bastion subnet size | `/26` |
| Bastion host exists | `bas-az104-uks-001` |
| Bastion public IP exists | `pip-bastion-uks-001` |
| VM public IP removed | No public IP on VM |
| VM private IP exists | Private IP still assigned |
| Bastion connection works | Browser session opens |

---

# Troubleshooting

## Bastion deployment fails

Check:

- Subnet name is exactly `AzureBastionSubnet`.
- Subnet size is `/26` or larger.
- Public IP is Standard SKU.
- Region supports Bastion.

## Cannot connect using Bastion

Check:

- VM is running.
- NSG allows required internal traffic.
- Credentials are correct.
- VM OS firewall allows RDP/SSH.
- VM is in the same VNet or a peered VNet supported by Bastion.

## Public IP still shows on VM

Check the NIC IP configuration and dissociate the public IP from the IP config.

## RDP/SSH direct connection fails

This is expected after removing the public IP.

---

# Security Best Practices

- Do not expose RDP or SSH directly to the internet.
- Use Bastion, VPN, or private connectivity.
- Restrict management access.
- Use NSGs to deny management ports from internet.
- Use Just-in-Time VM access where appropriate.
- Monitor sign-ins and administrative activity.

---

# Cost Control

Azure Bastion can incur cost while deployed.

For a lab environment, after completing screenshots and validation, consider deleting Bastion if you do not need it daily.

Delete Bastion:

```bash
az network bastion delete \
  --resource-group rg-az104-dev-uks-001 \
  --name bas-az104-uks-001
```

Delete Bastion public IP:

```bash
az network public-ip delete \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-bastion-uks-001
```

Keep the VNet and subnets for future labs.

---

# AZ-104 Exam Notes

You should know:

- Public IPs allow internet reachability.
- Private IPs are used within VNets.
- Azure Bastion provides secure RDP/SSH through the portal.
- Azure Bastion requires `AzureBastionSubnet`.
- Bastion subnet must be sized correctly.
- VMs can be managed without public IPs.
- Removing public IPs reduces attack surface.
