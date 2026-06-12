# Day 13 – Setup Guide: NAT Gateway + Secure Outbound Connectivity

## Objective

Deploy Azure NAT Gateway to allow private VMs to access the internet outbound without assigning public IP addresses directly to the VMs.

This guide includes:

- Azure CLI method
- Azure Portal method
- Outbound connectivity testing
- Public IP removal guidance
- Validation
- Troubleshooting
- Cost control

---

# Resource Naming

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| Region | `uksouth` |
| VNet | `vnet-nat-uks-001` |
| Subnet | `subnet-private` |
| NSG | `nsg-nat-private-001` |
| NAT Public IP | `pip-nat-uks-001` |
| NAT Gateway | `natgw-uks-001` |
| Test VM | `vm-nat-private-01` |

---

# IP Address Plan

| Network Component | Address Prefix |
|---|---|
| VNet | `10.50.0.0/16` |
| Private Subnet | `10.50.1.0/24` |

---

# Tags

| Tag | Value |
|---|---|
| env | dev |
| owner | your-name |
| project | az104-day13-nat-gateway |
| costCenter | learning |

---

# Important Concept

NAT Gateway is used for **outbound connectivity**.

It does not allow inbound internet traffic to private VMs.

```text
Private VM → Internet = Allowed through NAT Gateway
Internet → Private VM = Not allowed
```

This is exactly what you want for secure enterprise workloads.

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
  --tags env=dev owner="<your-name>" project=az104-day13-nat-gateway costCenter=learning
```

---

## Step 3 – Create VNet and Private Subnet

```bash
az network vnet create \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-nat-uks-001 \
  --location uksouth \
  --address-prefixes 10.50.0.0/16 \
  --subnet-name subnet-private \
  --subnet-prefixes 10.50.1.0/24 \
  --tags env=dev owner="<your-name>" project=az104-day13-nat-gateway costCenter=learning
```

---

## Step 4 – Create NSG for Private Subnet

```bash
az network nsg create \
  --resource-group rg-az104-dev-uks-001 \
  --name nsg-nat-private-001 \
  --location uksouth \
  --tags env=dev owner="<your-name>" project=az104-day13-nat-gateway costCenter=learning
```

---

## Step 5 – Associate NSG to Private Subnet

```bash
az network vnet subnet update \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-nat-uks-001 \
  --name subnet-private \
  --network-security-group nsg-nat-private-001
```

Professional note:

No inbound RDP or SSH rule is added because the VM should remain private. Use Azure Bastion or Run Command for administration.

---

## Step 6 – Create Standard Public IP for NAT Gateway

```bash
az network public-ip create \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-nat-uks-001 \
  --location uksouth \
  --sku Standard \
  --allocation-method Static \
  --tags env=dev owner="<your-name>" project=az104-day13-nat-gateway costCenter=learning
```

Why Standard Public IP?

NAT Gateway requires Standard SKU public IP addresses or public IP prefixes.

---

## Step 7 – Create NAT Gateway

```bash
az network nat gateway create \
  --resource-group rg-az104-dev-uks-001 \
  --name natgw-uks-001 \
  --location uksouth \
  --public-ip-addresses pip-nat-uks-001 \
  --idle-timeout 10 \
  --tags env=dev owner="<your-name>" project=az104-day13-nat-gateway costCenter=learning
```

---

## Step 8 – Associate NAT Gateway with Private Subnet

```bash
az network vnet subnet update \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-nat-uks-001 \
  --name subnet-private \
  --nat-gateway natgw-uks-001
```

Important:

NAT Gateway works at subnet level. Any supported VM in the associated subnet uses it for outbound internet traffic.

---

## Step 9 – Create Private Linux VM Without Public IP

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-nat-private-01 \
  --location uksouth \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --admin-username azureuser \
  --generate-ssh-keys \
  --vnet-name vnet-nat-uks-001 \
  --subnet subnet-private \
  --public-ip-address "" \
  --nsg ""
```

Why no public IP?

The goal is to prove that the VM can reach the internet outbound through NAT Gateway without being directly reachable from the internet.

---

## Step 10 – Validate VM Has No Public IP

```bash
az vm list-ip-addresses \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-nat-private-01 \
  --output table
```

Expected:

- Private IP exists.
- Public IP is empty.

---

## Step 11 – Test Outbound Connectivity Using Run Command

Because the VM has no public IP, use Azure Run Command:

```bash
az vm run-command invoke \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-nat-private-01 \
  --command-id RunShellScript \
  --scripts "curl -s https://ifconfig.me"
```

Expected:

The output should show the NAT Gateway public IP.

---

## Step 12 – Confirm NAT Public IP

```bash
az network public-ip show \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-nat-uks-001 \
  --query ipAddress \
  --output tsv
```

Compare this IP with the output from:

```bash
curl https://ifconfig.me
```

They should match.

---

## Step 13 – Test Package Update

```bash
az vm run-command invoke \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-nat-private-01 \
  --command-id RunShellScript \
  --scripts "sudo apt-get update -y"
```

If successful, the private VM has outbound internet access.

---

# Method 2 – Azure Portal

## Step 1 – Create VNet and Subnet

1. Sign in to Azure Portal.
2. Search for **Virtual networks**.
3. Click **Create**.
4. Configure:
   - Resource Group: `rg-az104-dev-uks-001`
   - Name: `vnet-nat-uks-001`
   - Region: `UK South`
5. IP Addresses:
   - Address space: `10.50.0.0/16`
   - Subnet name: `subnet-private`
   - Subnet range: `10.50.1.0/24`
6. Add tags.
7. Click **Review + create**.
8. Click **Create**.

---

## Step 2 – Create NSG

1. Search for **Network security groups**.
2. Click **Create**.
3. Configure:
   - Name: `nsg-nat-private-001`
   - Resource Group: `rg-az104-dev-uks-001`
   - Region: `UK South`
4. Add tags.
5. Click **Create**.

---

## Step 3 – Associate NSG to Private Subnet

1. Open `nsg-nat-private-001`.
2. Select **Subnets**.
3. Click **Associate**.
4. Select:
   - VNet: `vnet-nat-uks-001`
   - Subnet: `subnet-private`
5. Click **OK**.

Do not add inbound RDP or SSH rules for this lab.

---

## Step 4 – Create NAT Gateway

1. Search for **NAT gateways**.
2. Click **Create**.
3. Basics:
   - Resource Group: `rg-az104-dev-uks-001`
   - NAT gateway name: `natgw-uks-001`
   - Region: `UK South`
   - Availability zone: No zone or zone-redundant if available
   - Idle timeout: 10 minutes
4. Outbound IP:
   - Create a new public IP
   - Name: `pip-nat-uks-001`
   - SKU: Standard
5. Subnet:
   - Virtual network: `vnet-nat-uks-001`
   - Subnet: `subnet-private`
6. Add tags.
7. Click **Review + create**.
8. Click **Create**.

---

## Step 5 – Create Private Linux VM

1. Search for **Virtual machines**.
2. Click **Create** > **Azure virtual machine**.
3. Basics:
   - Name: `vm-nat-private-01`
   - Region: `UK South`
   - Image: Ubuntu Server 22.04 LTS
   - Size: Standard_B1s
   - Authentication: SSH public key
   - Username: `azureuser`
4. Networking:
   - VNet: `vnet-nat-uks-001`
   - Subnet: `subnet-private`
   - Public IP: None
   - NIC NSG: None or use subnet NSG
5. Add tags.
6. Click **Review + create**.
7. Click **Create**.

---

## Step 6 – Validate VM Public and Private IP

1. Open `vm-nat-private-01`.
2. On **Overview**, confirm:
   - Private IP exists
   - Public IP is blank or None

This proves the VM is private.

---

## Step 7 – Test Outbound Using Run Command

1. Open `vm-nat-private-01`.
2. Go to **Run command**.
3. Select **RunShellScript**.
4. Run:

```bash
curl -s https://ifconfig.me
```

5. Compare the output with the NAT Gateway public IP.

---

## Step 8 – Confirm NAT Gateway Association

1. Open `natgw-uks-001`.
2. Go to **Subnets**.
3. Confirm `subnet-private` is associated.
4. Go to **Outbound IP**.
5. Confirm `pip-nat-uks-001` is attached.

---

# Validation Checklist

| Check | Expected Result |
|---|---|
| NAT Gateway exists | `natgw-uks-001` |
| NAT Public IP exists | `pip-nat-uks-001` |
| NAT associated to subnet | `subnet-private` |
| VM public IP | None |
| VM private IP | Present |
| Outbound curl test | Returns NAT public IP |
| apt update | Successful |

---

# Troubleshooting

## VM cannot reach the internet

Check:

- NAT Gateway is associated with the correct subnet.
- NAT Gateway has a Standard Public IP.
- VM is in the NAT-associated subnet.
- NSG outbound rules are not blocking internet.
- DNS resolution works.

Test DNS:

```bash
az vm run-command invoke \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-nat-private-01 \
  --command-id RunShellScript \
  --scripts "nslookup microsoft.com"
```

## curl command fails

Try:

```bash
curl -s https://api.ipify.org
```

or:

```bash
curl -s https://ifconfig.io
```

## Outbound IP does not match NAT public IP

Check whether:

- The subnet is actually associated with NAT Gateway.
- The VM is in the correct subnet.
- Another outbound method is being used.

## Cannot SSH into the VM

This is expected because the VM has no public IP.

Use:

- Azure Bastion
- Run Command
- Private VPN
- Jump box

---

# Enterprise Best Practices

- Remove public IPs from workload VMs.
- Use NAT Gateway for predictable outbound access.
- Use Azure Firewall for inspected and controlled egress when required.
- Use NSGs to restrict unnecessary outbound traffic.
- Use Private Endpoints for Azure PaaS services where possible.
- Monitor outbound traffic with NSG Flow Logs and Azure Monitor.
- Use separate subnets for workloads with different egress requirements.

---

# NAT Gateway vs Other Options

| Option | Use Case |
|---|---|
| NAT Gateway | Scalable outbound internet for private subnets |
| Public IP on VM | Simple but exposes VM directly |
| Load Balancer outbound rule | Legacy or specific load-balanced scenarios |
| Azure Firewall | Centralized security inspection and policy control |
| Private Endpoint | Private access to Azure PaaS without internet |

---

# Cost Control

NAT Gateway and public IPs can incur cost.

If you are finished with the lab, delete NAT Gateway:

```bash
az network nat gateway delete \
  --resource-group rg-az104-dev-uks-001 \
  --name natgw-uks-001
```

Delete NAT public IP:

```bash
az network public-ip delete \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-nat-uks-001
```

Deallocate VM:

```bash
az vm deallocate \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-nat-private-01
```

---

# AZ-104 Exam Notes

You should know:

- NAT Gateway provides outbound connectivity.
- NAT Gateway is associated to a subnet.
- NAT Gateway does not provide inbound connectivity.
- NAT Gateway requires Standard Public IP or Public IP Prefix.
- Private VMs can access the internet without public IPs.
- Public IP removal reduces attack surface.
- Azure Bastion is used for private administration; NAT Gateway is used for outbound internet.
