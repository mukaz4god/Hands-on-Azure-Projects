# Day 09 – Setup Guide: Apply NSGs to Web and App Subnets

## Objective

Secure the Day 8 VNet by applying subnet-level NSGs:

- `subnet-web` should allow HTTP from the internet.
- `subnet-app` should not allow direct inbound internet traffic.

## Existing Resources

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| VNet | `vnet-az104-core-uks-001` |
| Web Subnet | `subnet-web` |
| App Subnet | `subnet-app` |
| Region | `uksouth` |

## Planned Resources

| Resource | Name |
|---|---|
| Web NSG | `nsg-web-001` |
| App NSG | `nsg-app-001` |

## Tags

| Tag | Value |
|---|---|
| env | dev |
| owner | your-name |
| project | az104-day09-nsg-security |
| costCenter | learning |

---

# Method 1 – Azure CLI

## Step 1 – Confirm Subscription

```bash
az account show --output table
```

If needed:

```bash
az account set --subscription "<subscription-name-or-id>"
```

## Step 2 – Validate Day 8 VNet and Subnets

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

## Step 3 – Create Web NSG

```bash
az network nsg create \
  --resource-group rg-az104-dev-uks-001 \
  --name nsg-web-001 \
  --location uksouth \
  --tags env=dev owner="<your-name>" project=az104-day09-nsg-security costCenter=learning
```

## Step 4 – Create App NSG

```bash
az network nsg create \
  --resource-group rg-az104-dev-uks-001 \
  --name nsg-app-001 \
  --location uksouth \
  --tags env=dev owner="<your-name>" project=az104-day09-nsg-security costCenter=learning
```

## Step 5 – Add HTTP Allow Rule to Web NSG

```bash
az network nsg rule create \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-web-001 \
  --name Allow-HTTP-Inbound \
  --priority 100 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes Internet \
  --source-port-ranges "*" \
  --destination-address-prefixes "*" \
  --destination-port-ranges 80 \
  --description "Allow HTTP traffic from Internet to web subnet"
```

Why this matters:

Only TCP 80 is allowed from the internet. Other inbound internet traffic is not explicitly allowed and will be denied by default NSG rules.

## Step 6 – Add Explicit Deny Internet Inbound Rule to Web NSG

```bash
az network nsg rule create \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-web-001 \
  --name Deny-Internet-Inbound \
  --priority 4096 \
  --direction Inbound \
  --access Deny \
  --protocol "*" \
  --source-address-prefixes Internet \
  --source-port-ranges "*" \
  --destination-address-prefixes "*" \
  --destination-port-ranges "*" \
  --description "Deny all other inbound Internet traffic"
```

Professional note:

Azure already has a default inbound deny rule. This explicit deny is added for learning and audit clarity.

## Step 7 – Add Explicit Deny Internet Inbound Rule to App NSG

```bash
az network nsg rule create \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-app-001 \
  --name Deny-Internet-Inbound \
  --priority 100 \
  --direction Inbound \
  --access Deny \
  --protocol "*" \
  --source-address-prefixes Internet \
  --source-port-ranges "*" \
  --destination-address-prefixes "*" \
  --destination-port-ranges "*" \
  --description "Block direct Internet inbound access to app subnet"
```

## Step 8 – Associate Web NSG with Web Subnet

```bash
az network vnet subnet update \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-az104-core-uks-001 \
  --name subnet-web \
  --network-security-group nsg-web-001
```

## Step 9 – Associate App NSG with App Subnet

```bash
az network vnet subnet update \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-az104-core-uks-001 \
  --name subnet-app \
  --network-security-group nsg-app-001
```

## Step 10 – Validate NSG Rules

```bash
az network nsg rule list \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-web-001 \
  --output table
```

```bash
az network nsg rule list \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-app-001 \
  --output table
```

## Step 11 – Validate Subnet Associations

```bash
az network vnet subnet show \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-az104-core-uks-001 \
  --name subnet-web \
  --query "networkSecurityGroup.id" \
  --output tsv
```

```bash
az network vnet subnet show \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-az104-core-uks-001 \
  --name subnet-app \
  --query "networkSecurityGroup.id" \
  --output tsv
```

---

# Method 2 – Azure Portal

## Step 1 – Create Web NSG

1. Sign in to Azure Portal.
2. Search for **Network security groups**.
3. Click **Create**.
4. Configure:
   - Resource Group: `rg-az104-dev-uks-001`
   - Name: `nsg-web-001`
   - Region: `UK South`
5. Add tags:
   - env = dev
   - owner = your-name
   - project = az104-day09-nsg-security
   - costCenter = learning
6. Click **Review + create**.
7. Click **Create**.

## Step 2 – Create App NSG

Repeat the same process with:

```text
nsg-app-001
```

## Step 3 – Add HTTP Rule to Web NSG

1. Open `nsg-web-001`.
2. Go to **Inbound security rules**.
3. Click **Add**.
4. Configure:

| Setting | Value |
|---|---|
| Source | Service Tag |
| Source service tag | Internet |
| Source port ranges | * |
| Destination | Any |
| Service | HTTP |
| Destination port ranges | 80 |
| Protocol | TCP |
| Action | Allow |
| Priority | 100 |
| Name | Allow-HTTP-Inbound |

5. Click **Add**.

## Step 4 – Add Deny Internet Rule to Web NSG

1. Still in `nsg-web-001`.
2. Click **Add**.
3. Configure:

| Setting | Value |
|---|---|
| Source | Service Tag |
| Source service tag | Internet |
| Source port ranges | * |
| Destination | Any |
| Destination port ranges | * |
| Protocol | Any |
| Action | Deny |
| Priority | 4096 |
| Name | Deny-Internet-Inbound |

4. Click **Add**.

## Step 5 – Add Deny Internet Rule to App NSG

1. Open `nsg-app-001`.
2. Go to **Inbound security rules**.
3. Click **Add**.
4. Configure:

| Setting | Value |
|---|---|
| Source | Service Tag |
| Source service tag | Internet |
| Source port ranges | * |
| Destination | Any |
| Destination port ranges | * |
| Protocol | Any |
| Action | Deny |
| Priority | 100 |
| Name | Deny-Internet-Inbound |

5. Click **Add**.

## Step 6 – Associate Web NSG to Web Subnet

1. Open `nsg-web-001`.
2. Select **Subnets**.
3. Click **Associate**.
4. Select:
   - Virtual network: `vnet-az104-core-uks-001`
   - Subnet: `subnet-web`
5. Click **OK**.

## Step 7 – Associate App NSG to App Subnet

1. Open `nsg-app-001`.
2. Select **Subnets**.
3. Click **Associate**.
4. Select:
   - Virtual network: `vnet-az104-core-uks-001`
   - Subnet: `subnet-app`
5. Click **OK**.

## Step 8 – Validate in Portal

1. Open each NSG.
2. Check **Inbound security rules**.
3. Check **Subnets** association.
4. Open the VNet and check each subnet shows the expected NSG.

---

# Validation Checklist

| Check | Expected Result |
|---|---|
| Web NSG created | `nsg-web-001` |
| App NSG created | `nsg-app-001` |
| HTTP rule exists | TCP 80 allowed on web NSG |
| Web NSG associated | `subnet-web` |
| App NSG associated | `subnet-app` |
| App subnet internet inbound | Denied |
| Tags applied | Present on NSGs |

---

# Troubleshooting

## HTTP traffic does not work later

Check:

- The VM is in `subnet-web`.
- IIS or web server is running.
- NSG allows TCP 80.
- VM OS firewall allows TCP 80.
- Public IP or Load Balancer exists if testing from internet.

## RDP or SSH stops working

This is expected if you did not allow 3389 or 22. For secure administration, use Bastion in Day 10.

## Rule not working as expected

Check NSG priority. Lower numbers are processed first.

## App subnet appears blocked

That is expected for direct internet inbound traffic. App servers should normally be private.

---

# Common Mistakes

- Using `Any` source and `Any` destination for allow rules.
- Creating an allow rule with a lower priority than a deny rule.
- Applying NSG to the wrong subnet.
- Forgetting OS-level firewall rules.
- Assuming NSG replaces all security controls.
- Leaving RDP/SSH exposed to internet.

---

# AZ-104 Exam Notes

You should know:

- NSGs are stateful.
- NSGs can be associated with subnets and NICs.
- Lower priority number wins.
- Default rules exist and cannot be deleted.
- NSGs filter Layer 3 and Layer 4 traffic.
- Application Security Groups can simplify rule management.
- Service Tags represent groups of Azure IP ranges.

---

# GitHub Evidence Checklist

Capture screenshots of:

- `nsg-web-001` overview.
- Web NSG inbound rules.
- `nsg-app-001` overview.
- App NSG inbound rules.
- Subnet association for web subnet.
- Subnet association for app subnet.
- CLI validation output.

Store in:

```text
/images/day09/
```

---

# Cleanup

Keep these resources for Day 10:

```text
vnet-az104-core-uks-001
subnet-web
subnet-app
nsg-web-001
nsg-app-001
```

NSGs are low-cost and should be kept for the next lab.
