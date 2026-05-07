# Day 06 – Setup Guide: Availability Sets and Availability Zones

## Objective

Deploy two Azure VM resilience patterns:

1. Two VMs inside an Availability Set.
2. Two VMs across different Availability Zones.

This guide includes both:

- **Azure CLI method**
- **Azure Portal method**

---

## Important Concept Before You Start

| Feature | Availability Set | Availability Zone |
|---|---|---|
| Protects against | Rack/host failure and planned maintenance | Datacenter-level failure |
| Uses | Fault domains and update domains | Physically separate zones |
| Scope | Logical VM grouping | Physical datacenter separation |
| Selected during VM creation? | Yes | Yes |
| Can be easily changed later? | No | No |

---

## Naming Convention

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| Region | `uksouth` |
| VNet | `vnet-ha-uks-001` |
| Subnet | `subnet-ha-web` |
| NSG | `nsg-ha-web-001` |
| Availability Set | `avset-web-uks-001` |
| Availability Set VM 1 | `vm-avset-web-01` |
| Availability Set VM 2 | `vm-avset-web-02` |
| Zone VM 1 | `vm-zone-web-01` |
| Zone VM 2 | `vm-zone-web-02` |

---

# Method 1 – Azure CLI

## Step 1 – Confirm Your Active Subscription

```bash
az account show --output table
```

If you have multiple subscriptions, select the correct one:

```bash
az account set --subscription "<subscription-name-or-id>"
```

### Why this matters

In real environments, engineers often have access to multiple subscriptions. Confirming the active subscription prevents accidental deployment into the wrong environment.

---

## Step 2 – Confirm the Resource Group Exists

```bash
az group show \
  --name rg-az104-dev-uks-001 \
  --output table
```

If it does not exist, create it:

```bash
az group create \
  --name rg-az104-dev-uks-001 \
  --location uksouth
```

---

## Step 3 – Create a Dedicated VNet and Subnet

```bash
az network vnet create \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-ha-uks-001 \
  --location uksouth \
  --address-prefix 10.20.0.0/16 \
  --subnet-name subnet-ha-web \
  --subnet-prefix 10.20.1.0/24
```

### Why this matters

A dedicated VNet keeps this lab clean and easier to explain in GitHub and interviews.

---

## Step 4 – Create a Network Security Group

```bash
az network nsg create \
  --resource-group rg-az104-dev-uks-001 \
  --name nsg-ha-web-001 \
  --location uksouth
```

---

## Step 5 – Allow RDP for Lab Access

```bash
az network nsg rule create \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-ha-web-001 \
  --name Allow-RDP \
  --priority 1000 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes Internet \
  --source-port-ranges "*" \
  --destination-address-prefixes "*" \
  --destination-port-ranges 3389
```

### Security note

For a real production environment, do not expose RDP to the whole internet. Better options include:

- Azure Bastion
- VPN
- Just-in-Time VM access
- Restricting source IP to your own public IP

---

## Step 6 – Associate the NSG to the Subnet

```bash
az network vnet subnet update \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-ha-uks-001 \
  --name subnet-ha-web \
  --network-security-group nsg-ha-web-001
```

### Why this matters

Associating the NSG to the subnet applies the same baseline traffic rules to all VMs deployed into that subnet.

---

# Part A – Create Availability Set and Deploy Two VMs

## Step 7 – Create the Availability Set

```bash
az vm availability-set create \
  --resource-group rg-az104-dev-uks-001 \
  --name avset-web-uks-001 \
  --location uksouth \
  --platform-fault-domain-count 2 \
  --platform-update-domain-count 5
```

### Explanation

- Fault domains separate VMs across different physical infrastructure.
- Update domains separate VMs during planned maintenance.
- Availability Set must be selected when the VM is created.

---

## Step 8 – Create the First VM in the Availability Set

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-avset-web-01 \
  --location uksouth \
  --availability-set avset-web-uks-001 \
  --image Win2019Datacenter \
  --size Standard_B2s \
  --admin-username azureuser \
  --admin-password "<StrongPassword123!>" \
  --vnet-name vnet-ha-uks-001 \
  --subnet subnet-ha-web \
  --public-ip-sku Standard
```

---

## Step 9 – Create the Second VM in the Same Availability Set

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-avset-web-02 \
  --location uksouth \
  --availability-set avset-web-uks-001 \
  --image Win2019Datacenter \
  --size Standard_B2s \
  --admin-username azureuser \
  --admin-password "<StrongPassword123!>" \
  --vnet-name vnet-ha-uks-001 \
  --subnet subnet-ha-web \
  --public-ip-sku Standard
```

### Important

Both VMs must use the same Availability Set. You cannot simply move an existing VM into an Availability Set later like changing a normal property.

---

## Step 10 – Validate Availability Set Deployment

List the VMs and show their Availability Set association:

```bash
az vm list \
  --resource-group rg-az104-dev-uks-001 \
  --query "[].{Name:name, AvailabilitySet:availabilitySet.id}" \
  --output table
```

Show Availability Set details:

```bash
az vm availability-set show \
  --resource-group rg-az104-dev-uks-001 \
  --name avset-web-uks-001 \
  --output table
```

---

# Part B – Deploy VMs Across Availability Zones

## Step 11 – Confirm Region Support

UK South supports Availability Zones for many services, but in real projects always confirm that your selected region and VM size support Availability Zones.

---

## Step 12 – Deploy VM in Zone 1

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-zone-web-01 \
  --location uksouth \
  --zone 1 \
  --image Win2019Datacenter \
  --size Standard_B2s \
  --admin-username azureuser \
  --admin-password "<StrongPassword123!>" \
  --vnet-name vnet-ha-uks-001 \
  --subnet subnet-ha-web \
  --public-ip-sku Standard
```

---

## Step 13 – Deploy VM in Zone 2

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-zone-web-02 \
  --location uksouth \
  --zone 2 \
  --image Win2019Datacenter \
  --size Standard_B2s \
  --admin-username azureuser \
  --admin-password "<StrongPassword123!>" \
  --vnet-name vnet-ha-uks-001 \
  --subnet subnet-ha-web \
  --public-ip-sku Standard
```

---

## Step 14 – Validate Zone Placement

```bash
az vm list \
  --resource-group rg-az104-dev-uks-001 \
  --query "[].{Name:name, Zone:zones}" \
  --output table
```

Expected result:

```text
vm-zone-web-01    1
vm-zone-web-02    2
```

---

## Step 15 – Cost Control: Deallocate VMs

When finished:

```bash
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-avset-web-01
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-avset-web-02
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-zone-web-01
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-zone-web-02
```

---

# Method 2 – Azure Portal

## Part A – Create an Availability Set

### Step 1 – Open Availability Sets

1. Sign in to the Azure Portal.
2. Search for **Availability sets**.
3. Click **Create**.

### Step 2 – Configure Availability Set

Use the following values:

| Setting | Value |
|---|---|
| Subscription | Your subscription |
| Resource Group | `rg-az104-dev-uks-001` |
| Name | `avset-web-uks-001` |
| Region | `UK South` |
| Fault domains | `2` |
| Update domains | `5` |

Click **Review + Create**, then **Create**.

### Why this matters

The Availability Set must exist before you deploy VMs into it.

---

## Part B – Create the First VM in the Availability Set

### Step 3 – Start VM Creation

1. Search for **Virtual machines**.
2. Click **Create**.
3. Select **Azure virtual machine**.

### Step 4 – Configure Basics

| Setting | Value |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| VM Name | `vm-avset-web-01` |
| Region | `UK South` |
| Availability options | `Availability set` |
| Availability set | `avset-web-uks-001` |
| Image | Windows Server 2019 Datacenter |
| Size | Standard_B2s |
| Username | `azureuser` |
| Password | Strong password |

### Step 5 – Configure Networking

Use:

| Setting | Value |
|---|---|
| Virtual network | `vnet-ha-uks-001` |
| Subnet | `subnet-ha-web` |
| Public IP | Create new |
| NSG | `nsg-ha-web-001` if available |
| Inbound port | RDP 3389 for lab only |

Click **Review + Create**, then **Create**.

---

## Part C – Create the Second VM in the Same Availability Set

Repeat the process with:

| Setting | Value |
|---|---|
| VM Name | `vm-avset-web-02` |
| Availability options | `Availability set` |
| Availability set | `avset-web-uks-001` |

---

## Part D – Validate Availability Set in Portal

1. Go to **Availability sets**.
2. Open `avset-web-uks-001`.
3. Confirm both VMs are listed:
   - `vm-avset-web-01`
   - `vm-avset-web-02`

---

# Portal: Deploy VMs Across Availability Zones

## Step 1 – Create Zone 1 VM

1. Go to **Virtual machines**.
2. Click **Create**.
3. Select **Azure virtual machine**.

Use:

| Setting | Value |
|---|---|
| VM Name | `vm-zone-web-01` |
| Region | `UK South` |
| Availability options | `Availability zone` |
| Availability zone | `Zone 1` |
| Image | Windows Server 2019 Datacenter |
| Size | Standard_B2s |
| VNet | `vnet-ha-uks-001` |
| Subnet | `subnet-ha-web` |

Click **Review + Create**, then **Create**.

---

## Step 2 – Create Zone 2 VM

Repeat with:

| Setting | Value |
|---|---|
| VM Name | `vm-zone-web-02` |
| Availability options | `Availability zone` |
| Availability zone | `Zone 2` |

---

## Step 3 – Validate Zone Placement in Portal

1. Go to **Virtual machines**.
2. Open `vm-zone-web-01`.
3. On the Overview page, confirm the VM is in Zone 1.
4. Open `vm-zone-web-02`.
5. Confirm the VM is in Zone 2.

---

# Troubleshooting

## Problem: Availability Set option is missing

Possible causes:

- VM already created.
- Wrong region selected.
- Portal UI layout changed.

Fix:

- Availability option must be selected during VM creation.
- Recreate the VM if required.

---

## Problem: Availability Zone option is unavailable

Possible causes:

- Region does not support zones for that VM type.
- Selected VM size is unavailable in that zone.

Fix:

- Try another VM SKU, such as `Standard_B2s`.
- Try another supported region.

---

## Problem: Deployment fails due to SKU unavailable

Fix:

Use a smaller common SKU:

```text
Standard_B1s
Standard_B2s
```

---

# GitHub Evidence Checklist

Store screenshots in:

```text
/images/day06/
```

Recommended screenshots:

- Availability Set overview.
- VMs listed inside Availability Set.
- VM deployed in Zone 1.
- VM deployed in Zone 2.
- CLI output showing Availability Set association.
- CLI output showing zone placement.
- Deallocated VM status after lab.

---

# AZ-104 Exam Notes

Know these points:

- Availability Sets use fault domains and update domains.
- Availability Zones are physically separate datacenter locations inside a region.
- Availability Set and Availability Zone choices are made at VM creation.
- Availability Zones provide stronger resilience than Availability Sets.
- Not every region or VM SKU supports Availability Zones.
- Availability Sets are useful where Zones are unavailable or when lower latency between VMs is required.
