# Day 07 – Setup Guide: Load-Balanced Web Application

## Objective

Deploy two Windows Server VMs running IIS behind a public Azure Load Balancer.

This guide includes:

- Azure CLI method.
- Azure Portal method.
- Validation.
- Troubleshooting.
- GitHub evidence checklist.

## Resource Naming

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| VNet | `vnet-lb-web-uks-001` |
| Subnet | `subnet-web` |
| NSG | `nsg-lb-web-001` |
| VM 1 | `vm-web-01` |
| VM 2 | `vm-web-02` |
| Load Balancer | `lb-web-uks-001` |
| Public IP | `pip-lb-web-uks-001` |
| Backend Pool | `bepool-web-001` |
| Health Probe | `probe-http-80` |
| Load Balancing Rule | `rule-http-80` |

## Tags

| Tag | Value |
|---|---|
| env | dev |
| owner | your-name |
| project | az104-day07-load-balanced-webapp |
| costCenter | learning |

# Method 1 – Azure CLI

## Step 1 – Confirm Subscription

```bash
az account show --output table
```

If needed:

```bash
az account set --subscription "<subscription-name-or-id>"
```

## Step 2 – Create or Confirm Resource Group

```bash
az group create \
  --name rg-az104-dev-uks-001 \
  --location uksouth \
  --tags env=dev owner="<your-name>" project=az104-day07-load-balanced-webapp costCenter=learning
```

## Step 3 – Create Virtual Network and Subnet

```bash
az network vnet create \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-lb-web-uks-001 \
  --location uksouth \
  --address-prefix 10.30.0.0/16 \
  --subnet-name subnet-web \
  --subnet-prefix 10.30.1.0/24 \
  --tags env=dev owner="<your-name>" project=az104-day07-load-balanced-webapp costCenter=learning
```

The VNet provides private network space for the backend web servers. The subnet groups the VMs into the same network segment.

## Step 4 – Create Network Security Group

```bash
az network nsg create \
  --resource-group rg-az104-dev-uks-001 \
  --name nsg-lb-web-001 \
  --location uksouth \
  --tags env=dev owner="<your-name>" project=az104-day07-load-balanced-webapp costCenter=learning
```

## Step 5 – Allow HTTP Traffic

```bash
az network nsg rule create \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-lb-web-001 \
  --name Allow-HTTP \
  --priority 1000 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes Internet \
  --source-port-ranges "*" \
  --destination-address-prefixes "*" \
  --destination-port-ranges 80
```

## Step 6 – Allow RDP for Lab Administration

```bash
az network nsg rule create \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-lb-web-001 \
  --name Allow-RDP-Lab \
  --priority 1010 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes Internet \
  --source-port-ranges "*" \
  --destination-address-prefixes "*" \
  --destination-port-ranges 3389
```

Security note: for production, replace `Internet` with your own public IP or use Azure Bastion.

## Step 7 – Associate NSG with Subnet

```bash
az network vnet subnet update \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-lb-web-uks-001 \
  --name subnet-web \
  --network-security-group nsg-lb-web-001
```

## Step 8 – Create Standard Public IP

```bash
az network public-ip create \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-lb-web-uks-001 \
  --location uksouth \
  --sku Standard \
  --allocation-method Static \
  --tags env=dev owner="<your-name>" project=az104-day07-load-balanced-webapp costCenter=learning
```

A Standard Load Balancer requires a Standard Public IP.

## Step 9 – Create Public Load Balancer

```bash
az network lb create \
  --resource-group rg-az104-dev-uks-001 \
  --name lb-web-uks-001 \
  --location uksouth \
  --sku Standard \
  --public-ip-address pip-lb-web-uks-001 \
  --frontend-ip-name fe-web-001 \
  --backend-pool-name bepool-web-001 \
  --tags env=dev owner="<your-name>" project=az104-day07-load-balanced-webapp costCenter=learning
```

## Step 10 – Create Health Probe

```bash
az network lb probe create \
  --resource-group rg-az104-dev-uks-001 \
  --lb-name lb-web-uks-001 \
  --name probe-http-80 \
  --protocol Tcp \
  --port 80
```

The probe checks if backend VMs are healthy. If a VM stops responding on port 80, the Load Balancer stops sending traffic to it.

## Step 11 – Create Load Balancing Rule

```bash
az network lb rule create \
  --resource-group rg-az104-dev-uks-001 \
  --lb-name lb-web-uks-001 \
  --name rule-http-80 \
  --protocol Tcp \
  --frontend-port 80 \
  --backend-port 80 \
  --frontend-ip-name fe-web-001 \
  --backend-pool-name bepool-web-001 \
  --probe-name probe-http-80
```

## Step 12 – Create VM 1

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-web-01 \
  --location uksouth \
  --image Win2019Datacenter \
  --size Standard_B2s \
  --admin-username azureuser \
  --admin-password "<StrongPassword123!>" \
  --vnet-name vnet-lb-web-uks-001 \
  --subnet subnet-web \
  --public-ip-address "" \
  --nsg "" \
  --tags env=dev owner="<your-name>" project=az104-day07-load-balanced-webapp costCenter=learning
```

## Step 13 – Create VM 2

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-web-02 \
  --location uksouth \
  --image Win2019Datacenter \
  --size Standard_B2s \
  --admin-username azureuser \
  --admin-password "<StrongPassword123!>" \
  --vnet-name vnet-lb-web-uks-001 \
  --subnet subnet-web \
  --public-ip-address "" \
  --nsg "" \
  --tags env=dev owner="<your-name>" project=az104-day07-load-balanced-webapp costCenter=learning
```

The VMs do not need individual public IPs because users access the application through the Load Balancer frontend IP.

## Step 14 – Add VM NICs to Backend Pool

List NICs:

```bash
az network nic list \
  --resource-group rg-az104-dev-uks-001 \
  --query "[].name" \
  --output table
```

Check IP configuration names:

```bash
az network nic ip-config list \
  --resource-group rg-az104-dev-uks-001 \
  --nic-name <nic-name> \
  --output table
```

Add VM 1 NIC:

```bash
az network nic ip-config address-pool add \
  --resource-group rg-az104-dev-uks-001 \
  --nic-name <vm-web-01-nic-name> \
  --ip-config-name <vm-web-01-ipconfig-name> \
  --lb-name lb-web-uks-001 \
  --address-pool bepool-web-001
```

Add VM 2 NIC:

```bash
az network nic ip-config address-pool add \
  --resource-group rg-az104-dev-uks-001 \
  --nic-name <vm-web-02-nic-name> \
  --ip-config-name <vm-web-02-ipconfig-name> \
  --lb-name lb-web-uks-001 \
  --address-pool bepool-web-001
```

## Step 15 – Install IIS on Both VMs Using Run Command

VM 1:

```bash
az vm run-command invoke \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-web-01 \
  --command-id RunPowerShellScript \
  --scripts "Install-WindowsFeature -name Web-Server -IncludeManagementTools; Set-Content -Path C:\inetpub\wwwroot\index.html -Value '<h1>AZ-104 Day 7</h1><p>Response from vm-web-01</p>'"
```

VM 2:

```bash
az vm run-command invoke \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-web-02 \
  --command-id RunPowerShellScript \
  --scripts "Install-WindowsFeature -name Web-Server -IncludeManagementTools; Set-Content -Path C:\inetpub\wwwroot\index.html -Value '<h1>AZ-104 Day 7</h1><p>Response from vm-web-02</p>'"
```

Run Command is useful because it allows configuration without direct RDP access.

## Step 16 – Get Load Balancer Public IP

```bash
az network public-ip show \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-lb-web-uks-001 \
  --query ipAddress \
  --output tsv
```

Open:

```text
http://<load-balancer-public-ip>
```

Refresh multiple times.

## Step 17 – Validate Backend Pool

```bash
az network lb address-pool show \
  --resource-group rg-az104-dev-uks-001 \
  --lb-name lb-web-uks-001 \
  --name bepool-web-001 \
  --output json
```

# Method 2 – Azure Portal

## Step 1 – Resource Group and Tags

1. Search for **Resource groups**.
2. Open `rg-az104-dev-uks-001`.
3. Go to **Tags**.
4. Add:
   - env = dev
   - owner = your-name
   - project = az104-day07-load-balanced-webapp
   - costCenter = learning
5. Click **Apply**.

## Step 2 – Create Virtual Network

1. Search for **Virtual networks**.
2. Click **Create**.
3. Basics:
   - Resource Group: `rg-az104-dev-uks-001`
   - Name: `vnet-lb-web-uks-001`
   - Region: UK South
4. IP addresses:
   - Address space: `10.30.0.0/16`
   - Subnet name: `subnet-web`
   - Subnet range: `10.30.1.0/24`
5. Add tags.
6. Click **Review + create** then **Create**.

## Step 3 – Create Network Security Group

1. Search for **Network security groups**.
2. Click **Create**.
3. Name: `nsg-lb-web-001`.
4. Resource Group: `rg-az104-dev-uks-001`.
5. Region: UK South.
6. Add tags.
7. Click **Review + create** then **Create**.

## Step 4 – Add Inbound Rules

Open `nsg-lb-web-001`.

Add HTTP:

- Source: Any
- Destination: Any
- Service: HTTP
- Destination port: 80
- Protocol: TCP
- Action: Allow
- Priority: 1000
- Name: Allow-HTTP

Add RDP for lab:

- Source: My IP address if available
- Destination port: 3389
- Protocol: TCP
- Action: Allow
- Priority: 1010
- Name: Allow-RDP-Lab

## Step 5 – Associate NSG to Subnet

1. Open `nsg-lb-web-001`.
2. Go to **Subnets**.
3. Click **Associate**.
4. Select:
   - VNet: `vnet-lb-web-uks-001`
   - Subnet: `subnet-web`
5. Click **OK**.

## Step 6 – Create Public IP

1. Search for **Public IP addresses**.
2. Click **Create**.
3. Name: `pip-lb-web-uks-001`.
4. SKU: Standard.
5. Assignment: Static.
6. Region: UK South.
7. Add tags.
8. Click **Create**.

## Step 7 – Create Load Balancer

1. Search for **Load balancers**.
2. Click **Create**.
3. Basics:
   - Name: `lb-web-uks-001`
   - SKU: Standard
   - Type: Public
   - Region: UK South
4. Frontend IP:
   - Name: `fe-web-001`
   - Public IP: `pip-lb-web-uks-001`
5. Backend pool:
   - Name: `bepool-web-001`
6. Health probe:
   - Name: `probe-http-80`
   - Protocol: TCP
   - Port: 80
7. Load balancing rule:
   - Name: `rule-http-80`
   - Frontend port: 80
   - Backend port: 80
   - Backend pool: `bepool-web-001`
   - Health probe: `probe-http-80`
8. Add tags and create.

## Step 8 – Create VM 1

1. Search for **Virtual machines**.
2. Click **Create**.
3. Name: `vm-web-01`.
4. Region: UK South.
5. Image: Windows Server 2019 Datacenter.
6. Size: Standard_B2s.
7. Username/password: choose secure credentials.
8. Networking:
   - VNet: `vnet-lb-web-uks-001`
   - Subnet: `subnet-web`
   - Public IP: None
   - NIC NSG: None or use subnet NSG
9. Add tags and create.

## Step 9 – Create VM 2

Repeat with name `vm-web-02`.

## Step 10 – Add VMs to Backend Pool

1. Open `lb-web-uks-001`.
2. Go to **Backend pools**.
3. Open `bepool-web-001`.
4. Add `vm-web-01`.
5. Add `vm-web-02`.
6. Save.

## Step 11 – Install IIS Using Run Command

For each VM:

1. Open VM.
2. Go to **Run command**.
3. Choose **RunPowerShellScript**.
4. Run the IIS install script.

VM 1:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "<h1>AZ-104 Day 7</h1><p>Response from vm-web-01</p>"
```

VM 2:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "<h1>AZ-104 Day 7</h1><p>Response from vm-web-02</p>"
```

## Step 12 – Test

Browse to:

```text
http://<load-balancer-public-ip>
```

# Validation Checklist

| Check | Expected Result |
|---|---|
| VMs running | vm-web-01 and vm-web-02 running |
| IIS installed | Web page available |
| NSG HTTP rule | Port 80 allowed |
| Load Balancer frontend | Public IP assigned |
| Backend pool | Both VMs added |
| Health probe | Backend VMs healthy |
| Tags | Applied to resources |

# Troubleshooting

## Website does not load

Check IIS, NSG port 80, backend pool, health probe, and load balancing rule.

## Only one VM responds

Check that both VMs are in the backend pool and IIS is running on both.

## RDP not working

If VMs do not have public IPs, use Azure Bastion, Run Command, VPN, or a temporary public IP for lab use only.

# Cleanup

```bash
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-web-01
az vm deallocate --resource-group rg-az104-dev-uks-001 --name vm-web-02
```
