# Day 02 – Setup Guide: Windows Virtual Machine Deployment

## Prerequisites

- Completed Day 01
- Existing Resource Group: `rg-az104-dev-uks-001`

## Step 1 – Create Virtual Network

```bash
az network vnet create \
  --name vnet-az104-dev-001 \
  --resource-group rg-az104-dev-uks-001 \
  --address-prefix 10.0.0.0/16 \
  --subnet-name subnet-web \
  --subnet-prefix 10.0.1.0/24
```

## Step 2 – Create NSG

```bash
az network nsg create \
  --name nsg-rdp-001 \
  --resource-group rg-az104-dev-uks-001
```

## Step 3 – Allow RDP (3389)

```bash
az network nsg rule create \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-rdp-001 \
  --name Allow-RDP \
  --protocol Tcp \
  --priority 1000 \
  --destination-port-range 3389 \
  --access Allow
```

## Step 4 – Create Public IP

```bash
az network public-ip create \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-vm-001
```

## Step 5 – Create NIC

```bash
az network nic create \
  --resource-group rg-az104-dev-uks-001 \
  --name nic-vm-001 \
  --vnet-name vnet-az104-dev-001 \
  --subnet subnet-web \
  --network-security-group nsg-rdp-001 \
  --public-ip-address pip-vm-001
```

## Step 6 – Create Windows VM

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-win-001 \
  --nics nic-vm-001 \
  --image Win2019Datacenter \
  --admin-username azureuser \
  --admin-password <YourPassword123!>
```

## Step 7 – Connect via RDP

Get public IP:

```bash
az vm list-ip-addresses --name vm-win-001 --output table
```

Connect using Remote Desktop.

## Validation

- VM status = Running
- NSG rule exists
- RDP connection works
