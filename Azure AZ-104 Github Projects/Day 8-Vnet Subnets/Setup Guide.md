# Day 08 – Setup Guide: Create VNet and Subnets

## Objective

Create a foundational Azure Virtual Network with two subnets:

- `subnet-web` – `10.0.1.0/24`
- `subnet-app` – `10.0.2.0/24`

This guide includes Azure CLI and Azure Portal steps.

## Resource Naming

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| Virtual Network | `vnet-az104-core-uks-001` |
| Web Subnet | `subnet-web` |
| App Subnet | `subnet-app` |
| Region | `UK South` |

## IP Address Plan

| Network Component | Address Prefix |
|---|---|
| VNet | `10.0.0.0/16` |
| Web Subnet | `10.0.1.0/24` |
| App Subnet | `10.0.2.0/24` |

`10.0.0.0/16` gives a large private address space from `10.0.0.0` to `10.0.255.255`.

A `/24` subnet gives 256 IP addresses, but Azure reserves 5 IP addresses in every subnet, leaving 251 usable IP addresses.

## Tags

| Tag | Value |
|---|---|
| env | dev |
| owner | your-name |
| project | az104-day08-vnet-subnets |
| costCenter | learning |

# Method 1 – Azure CLI

## Step 1 – Confirm Active Subscription

```bash
az account show --output table
```

If you have more than one subscription:

```bash
az account set --subscription "<subscription-name-or-id>"
```

## Step 2 – Confirm Resource Group Exists

```bash
az group show \
  --name rg-az104-dev-uks-001 \
  --output table
```

If it does not exist:

```bash
az group create \
  --name rg-az104-dev-uks-001 \
  --location uksouth \
  --tags env=dev owner="<your-name>" project=az104-day08-vnet-subnets costCenter=learning
```

## Step 3 – Create VNet with Web Subnet

```bash
az network vnet create \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-az104-core-uks-001 \
  --location uksouth \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name subnet-web \
  --subnet-prefixes 10.0.1.0/24 \
  --tags env=dev owner="<your-name>" project=az104-day08-vnet-subnets costCenter=learning
```

This creates the VNet and the first subnet.

## Step 4 – Add App Subnet

```bash
az network vnet subnet create \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-az104-core-uks-001 \
  --name subnet-app \
  --address-prefixes 10.0.2.0/24
```

Separate subnets allow you to apply different security rules later.

## Step 5 – Validate VNet

```bash
az network vnet show \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-az104-core-uks-001 \
  --query "{Name:name, Location:location, AddressSpace:addressSpace.addressPrefixes}" \
  --output table
```

## Step 6 – Validate Subnets

```bash
az network vnet subnet list \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-az104-core-uks-001 \
  --query "[].{Name:name, Prefix:addressPrefix}" \
  --output table
```

Expected:

```text
Name          Prefix
------------  ------------
subnet-web    10.0.1.0/24
subnet-app    10.0.2.0/24
```

## Step 7 – Validate Tags

```bash
az network vnet show \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-az104-core-uks-001 \
  --query tags \
  --output table
```

# Method 2 – Azure Portal

## Step 1 – Open Virtual Networks

1. Sign in to the Azure Portal.
2. In the search bar, type **Virtual networks**.
3. Select **Virtual networks**.
4. Click **Create**.

## Step 2 – Configure Basics

| Setting | Value |
|---|---|
| Subscription | Your subscription |
| Resource Group | `rg-az104-dev-uks-001` |
| Name | `vnet-az104-core-uks-001` |
| Region | `UK South` |

Click **Next: IP Addresses**.

## Step 3 – Configure VNet Address Space

On the **IP Addresses** tab, set IPv4 address space to:

```text
10.0.0.0/16
```

If a default address space already exists, edit or remove it.

## Step 4 – Create Web Subnet

1. Click **Add a subnet**.
2. Configure:

| Setting | Value |
|---|---|
| Subnet name | `subnet-web` |
| Starting address | `10.0.1.0` |
| Subnet size | `/24` |
| Address range | `10.0.1.0/24` |

3. Click **Add**.

## Step 5 – Create App Subnet

1. Click **Add a subnet** again.
2. Configure:

| Setting | Value |
|---|---|
| Subnet name | `subnet-app` |
| Starting address | `10.0.2.0` |
| Subnet size | `/24` |
| Address range | `10.0.2.0/24` |

3. Click **Add**.

## Step 6 – Add Tags

Go to the **Tags** tab and add:

| Name | Value |
|---|---|
| env | dev |
| owner | your-name |
| project | az104-day08-vnet-subnets |
| costCenter | learning |

## Step 7 – Review and Create

1. Review the configuration.
2. Confirm:
   - VNet address space is `10.0.0.0/16`.
   - `subnet-web` is `10.0.1.0/24`.
   - `subnet-app` is `10.0.2.0/24`.
3. Click **Create**.

## Step 8 – Validate in Portal

1. Open `vnet-az104-core-uks-001`.
2. Go to **Address space**.
3. Confirm `10.0.0.0/16`.
4. Go to **Subnets**.
5. Confirm `subnet-web` and `subnet-app`.
6. Go to **Tags** and confirm all tags.

# Validation Checklist

| Check | Expected Result |
|---|---|
| VNet exists | `vnet-az104-core-uks-001` |
| VNet address space | `10.0.0.0/16` |
| Web subnet exists | `subnet-web` |
| Web subnet prefix | `10.0.1.0/24` |
| App subnet exists | `subnet-app` |
| App subnet prefix | `10.0.2.0/24` |
| Tags applied | env, owner, project, costCenter |

# Troubleshooting

## Subnet address range overlaps

Use a unique subnet range within the VNet, for example `10.0.3.0/24`.

## Address prefix is outside the VNet range

Use subnet ranges beginning with `10.0.x.0/24`.

## VNet name already exists

Use the existing VNet or choose a new name such as `vnet-az104-core-uks-002`.

## Portal shows fewer usable IPs than expected

Azure reserves 5 IP addresses in every subnet. This is normal.

# Common Mistakes

- Creating all workloads in one subnet.
- Using overlapping address ranges.
- Choosing a VNet range that conflicts with on-premises networks.
- Forgetting to apply tags.
- Making subnets too small for future growth.
- Not planning private endpoint or firewall subnet requirements early.

# AZ-104 Exam Notes

You should know:

- VNets provide private network isolation in Azure.
- Subnets divide a VNet into smaller address ranges.
- Azure reserves 5 IP addresses in each subnet.
- NSGs can be associated to subnets and/or NICs.
- Subnetting supports security segmentation.
- Address spaces should not overlap if networks will be peered.
- VNet and VM must be in the same region for direct deployment.

# Cleanup / Cost Control

This lab is very low cost because VNets and subnets do not normally incur direct compute charges.

Keep this network for Day 9 NSG practice.

To delete it:

```bash
az network vnet delete \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-az104-core-uks-001
```
