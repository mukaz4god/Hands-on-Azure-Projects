# Day 11 – Setup Guide: Create and Test VNet Peering

## Objective

Create two Azure Virtual Networks, configure bidirectional peering, and validate private connectivity.

This guide includes:

- Azure CLI method
- Azure Portal method
- Optional VM connectivity test
- Validation
- Troubleshooting
- Cleanup and cost control

---

# Resource Naming

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| Region | `uksouth` |
| Web VNet | `vnet-web-uks-001` |
| Web Subnet | `subnet-web` |
| App VNet | `vnet-app-uks-001` |
| App Subnet | `subnet-app` |
| Web-to-App Peering | `peer-web-to-app` |
| App-to-Web Peering | `peer-app-to-web` |

---

# IP Address Plan

| Network | Address Space | Subnet |
|---|---|---|
| Web VNet | `10.11.0.0/16` | `10.11.1.0/24` |
| App VNet | `10.12.0.0/16` | `10.12.1.0/24` |

Important:

These ranges do not overlap. Overlapping address ranges prevent successful routing between VNets.

---

# Tags

Apply these tags where supported:

| Tag | Value |
|---|---|
| env | dev |
| owner | your-name |
| project | az104-day11-vnet-peering |
| costCenter | learning |

---

# Method 1 – Azure CLI

## Step 1 – Confirm Active Subscription

```bash
az account show --output table
```

If required:

```bash
az account set --subscription "<subscription-name-or-id>"
```

---

## Step 2 – Create or Confirm Resource Group

```bash
az group create \
  --name rg-az104-dev-uks-001 \
  --location uksouth \
  --tags env=dev owner="<your-name>" project=az104-day11-vnet-peering costCenter=learning
```

---

## Step 3 – Create Web VNet

```bash
az network vnet create \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-web-uks-001 \
  --location uksouth \
  --address-prefixes 10.11.0.0/16 \
  --subnet-name subnet-web \
  --subnet-prefixes 10.11.1.0/24 \
  --tags env=dev owner="<your-name>" project=az104-day11-vnet-peering costCenter=learning
```

---

## Step 4 – Create App VNet

```bash
az network vnet create \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-app-uks-001 \
  --location uksouth \
  --address-prefixes 10.12.0.0/16 \
  --subnet-name subnet-app \
  --subnet-prefixes 10.12.1.0/24 \
  --tags env=dev owner="<your-name>" project=az104-day11-vnet-peering costCenter=learning
```

---

## Step 5 – Get VNet Resource IDs

```bash
WEB_VNET_ID=$(az network vnet show \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-web-uks-001 \
  --query id \
  --output tsv)

APP_VNET_ID=$(az network vnet show \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-app-uks-001 \
  --query id \
  --output tsv)
```

---

## Step 6 – Create Web-to-App Peering

```bash
az network vnet peering create \
  --resource-group rg-az104-dev-uks-001 \
  --name peer-web-to-app \
  --vnet-name vnet-web-uks-001 \
  --remote-vnet $APP_VNET_ID \
  --allow-vnet-access
```

Explanation:

This allows resources in `vnet-web-uks-001` to communicate with resources in `vnet-app-uks-001`.

---

## Step 7 – Create App-to-Web Peering

```bash
az network vnet peering create \
  --resource-group rg-az104-dev-uks-001 \
  --name peer-app-to-web \
  --vnet-name vnet-app-uks-001 \
  --remote-vnet $WEB_VNET_ID \
  --allow-vnet-access
```

Why both directions?

VNet peering needs to exist in both directions for full bidirectional communication.

---

## Step 8 – Validate Peering Status

```bash
az network vnet peering list \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-web-uks-001 \
  --query "[].{Name:name, State:peeringState, RemoteVNet:remoteVirtualNetwork.id}" \
  --output table
```

```bash
az network vnet peering list \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-app-uks-001 \
  --query "[].{Name:name, State:peeringState, RemoteVNet:remoteVirtualNetwork.id}" \
  --output table
```

Expected state:

```text
Connected
```

---

# Optional Connectivity Test with Linux VMs

This section is optional but strongly recommended because it proves private connectivity.

## Step 9 – Create NSGs for Test Connectivity

Create NSG for web test VM:

```bash
az network nsg create \
  --resource-group rg-az104-dev-uks-001 \
  --name nsg-web-peer-test-001 \
  --location uksouth
```

Create NSG for app test VM:

```bash
az network nsg create \
  --resource-group rg-az104-dev-uks-001 \
  --name nsg-app-peer-test-001 \
  --location uksouth
```

Allow ICMP is not directly configured as a simple Azure NSG service like HTTP/RDP, so for connectivity testing use TCP-based tests such as SSH or a simple web server.

## Step 10 – Create Linux VM in Web VNet

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-web-peer-01 \
  --location uksouth \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --admin-username azureuser \
  --generate-ssh-keys \
  --vnet-name vnet-web-uks-001 \
  --subnet subnet-web \
  --nsg nsg-web-peer-test-001
```

## Step 11 – Create Linux VM in App VNet

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-app-peer-01 \
  --location uksouth \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --admin-username azureuser \
  --generate-ssh-keys \
  --vnet-name vnet-app-uks-001 \
  --subnet subnet-app \
  --nsg nsg-app-peer-test-001
```

## Step 12 – Get Private IP of App VM

```bash
APP_PRIVATE_IP=$(az vm list-ip-addresses \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-app-peer-01 \
  --query "[0].virtualMachine.network.privateIpAddresses[0]" \
  --output tsv)

echo $APP_PRIVATE_IP
```

## Step 13 – Install a Simple Web Server on App VM

```bash
az vm run-command invoke \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-app-peer-01 \
  --command-id RunShellScript \
  --scripts "sudo apt-get update && sudo apt-get install -y nginx && echo 'Hello from app VNet' | sudo tee /var/www/html/index.html"
```

## Step 14 – Allow HTTP on App VM NSG from Web VNet Range

```bash
az network nsg rule create \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-app-peer-test-001 \
  --name Allow-HTTP-From-Web-VNet \
  --priority 100 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes 10.11.0.0/16 \
  --source-port-ranges "*" \
  --destination-address-prefixes "*" \
  --destination-port-ranges 80
```

## Step 15 – Test Connectivity from Web VM to App VM

Run this command on `vm-web-peer-01` using Run Command:

```bash
az vm run-command invoke \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-web-peer-01 \
  --command-id RunShellScript \
  --scripts "curl http://$APP_PRIVATE_IP"
```

Expected output:

```text
Hello from app VNet
```

---

# Method 2 – Azure Portal

## Step 1 – Create Web VNet

1. Sign in to Azure Portal.
2. Search for **Virtual networks**.
3. Click **Create**.
4. Configure:
   - Resource Group: `rg-az104-dev-uks-001`
   - Name: `vnet-web-uks-001`
   - Region: `UK South`
5. IP Addresses:
   - Address space: `10.11.0.0/16`
   - Subnet name: `subnet-web`
   - Subnet range: `10.11.1.0/24`
6. Add tags.
7. Click **Review + create**.
8. Click **Create**.

## Step 2 – Create App VNet

Repeat the process:

| Setting | Value |
|---|---|
| Name | `vnet-app-uks-001` |
| Address space | `10.12.0.0/16` |
| Subnet | `subnet-app` |
| Subnet prefix | `10.12.1.0/24` |

## Step 3 – Create Peering from Web to App

1. Open `vnet-web-uks-001`.
2. Go to **Peerings**.
3. Click **+ Add**.
4. Configure:
   - Peering link name: `peer-web-to-app`
   - Remote virtual network: `vnet-app-uks-001`
   - Allow traffic to remote virtual network: Enabled
   - Allow traffic forwarded from remote virtual network: Disabled for this lab
   - Allow gateway or route server in remote VNet: Disabled
5. Click **Add**.

## Step 4 – Create Peering from App to Web

1. Open `vnet-app-uks-001`.
2. Go to **Peerings**.
3. Click **+ Add**.
4. Configure:
   - Peering link name: `peer-app-to-web`
   - Remote virtual network: `vnet-web-uks-001`
   - Allow traffic to remote virtual network: Enabled
5. Click **Add**.

## Step 5 – Validate Portal Peering Status

1. Open each VNet.
2. Go to **Peerings**.
3. Confirm status shows:

```text
Connected
```

---

# Validation Checklist

| Check | Expected Result |
|---|---|
| Web VNet exists | `vnet-web-uks-001` |
| App VNet exists | `vnet-app-uks-001` |
| Address ranges do not overlap | True |
| Web-to-App peering | Connected |
| App-to-Web peering | Connected |
| Private connectivity test | Successful if optional VMs deployed |

---

# Troubleshooting

## Peering fails

Check:

- Address spaces do not overlap.
- You have permission on both VNets.
- VNets exist in the same or supported regions.
- Remote VNet ID is correct.

## Peering status is Initiated instead of Connected

This usually means only one direction was created. Create the reverse peering.

## VM connectivity fails

Check:

- NSG rules.
- VM OS firewall.
- Target service is listening.
- You are using private IP, not public IP.
- Peering status is Connected.

## Cannot ping between VMs

ICMP may be blocked by OS firewall or NSG. Use TCP tests such as `curl`, `Test-NetConnection`, or SSH instead.

---

# Enterprise Best Practices

- Never overlap address spaces.
- Use hub-and-spoke architecture for larger environments.
- Use Azure Firewall or NVAs for centralized inspection.
- Use route tables for controlled traffic paths.
- Use Private DNS for name resolution between VNets.
- Use Network Watcher for troubleshooting.
- Document IP address plans early.

---

# Cost Control

VNets and peering are generally low cost, but test VMs cost money.

After testing:

```bash
az vm deallocate \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-web-peer-01

az vm deallocate \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-app-peer-01
```

To delete test VMs:

```bash
az vm delete \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-web-peer-01 \
  --yes

az vm delete \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-app-peer-01 \
  --yes
```

Keep the VNets and peering if continuing future networking labs.
