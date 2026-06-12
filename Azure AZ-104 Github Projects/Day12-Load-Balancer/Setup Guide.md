# Day 12 – Setup Guide: Deploy Azure Load Balancer with 2 Web VMs

## Objective

Deploy an Azure Standard Load Balancer with two backend Windows web servers running IIS.

This guide includes:

- Azure CLI method
- Azure Portal method
- Validation
- Troubleshooting
- Cleanup and cost control

---

# Resource Naming

| Resource | Name |
|---|---|
| Resource Group | `rg-az104-dev-uks-001` |
| Region | `uksouth` |
| VNet | `vnet-lb-uks-001` |
| Subnet | `subnet-web` |
| NSG | `nsg-lb-web-001` |
| Public IP | `pip-lb-uks-001` |
| Load Balancer | `lb-web-uks-001` |
| Frontend IP | `feip-web-001` |
| Backend Pool | `bepool-web-001` |
| Health Probe | `probe-http-80` |
| Load Rule | `rule-http-80` |
| VM 1 | `vm-lb-web-01` |
| VM 2 | `vm-lb-web-02` |

---

# Tags

| Tag | Value |
|---|---|
| env | dev |
| owner | your-name |
| project | az104-day12-load-balancer |
| costCenter | learning |

---

# Method 1 – Azure CLI

## Step 1 – Confirm Subscription

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
  --tags env=dev owner="<your-name>" project=az104-day12-load-balancer costCenter=learning
```

---

## Step 3 – Create VNet and Subnet

```bash
az network vnet create \
  --resource-group rg-az104-dev-uks-001 \
  --name vnet-lb-uks-001 \
  --location uksouth \
  --address-prefixes 10.40.0.0/16 \
  --subnet-name subnet-web \
  --subnet-prefixes 10.40.1.0/24 \
  --tags env=dev owner="<your-name>" project=az104-day12-load-balancer costCenter=learning
```

Why this matters:

The backend VMs will sit inside this subnet. The load balancer forwards traffic to backend NICs in this network.

---

## Step 4 – Create NSG

```bash
az network nsg create \
  --resource-group rg-az104-dev-uks-001 \
  --name nsg-lb-web-001 \
  --location uksouth \
  --tags env=dev owner="<your-name>" project=az104-day12-load-balancer costCenter=learning
```

---

## Step 5 – Allow HTTP Inbound

```bash
az network nsg rule create \
  --resource-group rg-az104-dev-uks-001 \
  --nsg-name nsg-lb-web-001 \
  --name Allow-HTTP \
  --priority 100 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes Internet \
  --source-port-ranges "*" \
  --destination-address-prefixes "*" \
  --destination-port-ranges 80
```

---

## Step 6 – Associate NSG to Subnet

```bash
az network vnet subnet update \
  --resource-group rg-az104-dev-uks-001 \
  --vnet-name vnet-lb-uks-001 \
  --name subnet-web \
  --network-security-group nsg-lb-web-001
```

---

## Step 7 – Create Standard Public IP for Load Balancer

```bash
az network public-ip create \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-lb-uks-001 \
  --location uksouth \
  --sku Standard \
  --allocation-method Static \
  --tags env=dev owner="<your-name>" project=az104-day12-load-balancer costCenter=learning
```

Important:

A Standard Load Balancer requires a Standard Public IP.

---

## Step 8 – Create Standard Load Balancer

```bash
az network lb create \
  --resource-group rg-az104-dev-uks-001 \
  --name lb-web-uks-001 \
  --location uksouth \
  --sku Standard \
  --public-ip-address pip-lb-uks-001 \
  --frontend-ip-name feip-web-001 \
  --backend-pool-name bepool-web-001 \
  --tags env=dev owner="<your-name>" project=az104-day12-load-balancer costCenter=learning
```

---

## Step 9 – Create Health Probe

```bash
az network lb probe create \
  --resource-group rg-az104-dev-uks-001 \
  --lb-name lb-web-uks-001 \
  --name probe-http-80 \
  --protocol Tcp \
  --port 80
```

Why this matters:

The health probe checks whether the backend VMs are listening on port 80. If a VM is unhealthy, the load balancer stops sending traffic to it.

---

## Step 10 – Create Load Balancing Rule

```bash
az network lb rule create \
  --resource-group rg-az104-dev-uks-001 \
  --lb-name lb-web-uks-001 \
  --name rule-http-80 \
  --protocol Tcp \
  --frontend-port 80 \
  --backend-port 80 \
  --frontend-ip-name feip-web-001 \
  --backend-pool-name bepool-web-001 \
  --probe-name probe-http-80
```

---

## Step 11 – Create VM 1

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-lb-web-01 \
  --location uksouth \
  --image Win2019Datacenter \
  --size Standard_B2s \
  --admin-username azureuser \
  --admin-password "<StrongPassword123!>" \
  --vnet-name vnet-lb-uks-001 \
  --subnet subnet-web \
  --public-ip-address "" \
  --nsg "" \
  --tags env=dev owner="<your-name>" project=az104-day12-load-balancer costCenter=learning
```

---

## Step 12 – Create VM 2

```bash
az vm create \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-lb-web-02 \
  --location uksouth \
  --image Win2019Datacenter \
  --size Standard_B2s \
  --admin-username azureuser \
  --admin-password "<StrongPassword123!>" \
  --vnet-name vnet-lb-uks-001 \
  --subnet subnet-web \
  --public-ip-address "" \
  --nsg "" \
  --tags env=dev owner="<your-name>" project=az104-day12-load-balancer costCenter=learning
```

Professional note:

The backend VMs do not need public IP addresses. Users should access the application through the Load Balancer frontend IP.

---

## Step 13 – Identify VM NIC Names and IP Config Names

List NICs:

```bash
az network nic list \
  --resource-group rg-az104-dev-uks-001 \
  --query "[].name" \
  --output table
```

For each NIC:

```bash
az network nic ip-config list \
  --resource-group rg-az104-dev-uks-001 \
  --nic-name <nic-name> \
  --query "[].{Name:name, PrivateIP:privateIPAddress}" \
  --output table
```

---

## Step 14 – Add VM NICs to Backend Pool

For VM 1:

```bash
az network nic ip-config address-pool add \
  --resource-group rg-az104-dev-uks-001 \
  --nic-name <vm-lb-web-01-nic-name> \
  --ip-config-name <vm-lb-web-01-ipconfig-name> \
  --lb-name lb-web-uks-001 \
  --address-pool bepool-web-001
```

For VM 2:

```bash
az network nic ip-config address-pool add \
  --resource-group rg-az104-dev-uks-001 \
  --nic-name <vm-lb-web-02-nic-name> \
  --ip-config-name <vm-lb-web-02-ipconfig-name> \
  --lb-name lb-web-uks-001 \
  --address-pool bepool-web-001
```

Why placeholders?

Azure sometimes generates NIC/IP config names differently depending on CLI version and VM creation settings. Listing them first avoids mistakes.

---

## Step 15 – Install IIS on VM 1

```bash
az vm run-command invoke \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-lb-web-01 \
  --command-id RunPowerShellScript \
  --scripts "Install-WindowsFeature -name Web-Server -IncludeManagementTools; Set-Content -Path C:\inetpub\wwwroot\index.html -Value '<h1>AZ-104 Day 12</h1><p>Response from vm-lb-web-01</p>'"
```

---

## Step 16 – Install IIS on VM 2

```bash
az vm run-command invoke \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-lb-web-02 \
  --command-id RunPowerShellScript \
  --scripts "Install-WindowsFeature -name Web-Server -IncludeManagementTools; Set-Content -Path C:\inetpub\wwwroot\index.html -Value '<h1>AZ-104 Day 12</h1><p>Response from vm-lb-web-02</p>'"
```

---

## Step 17 – Get Load Balancer Public IP

```bash
az network public-ip show \
  --resource-group rg-az104-dev-uks-001 \
  --name pip-lb-uks-001 \
  --query ipAddress \
  --output tsv
```

Open in browser:

```text
http://<load-balancer-public-ip>
```

Refresh several times to observe traffic distribution.

---

## Step 18 – Validate Load Balancer Backend Pool

```bash
az network lb address-pool show \
  --resource-group rg-az104-dev-uks-001 \
  --lb-name lb-web-uks-001 \
  --name bepool-web-001 \
  --output json
```

---

# Method 2 – Azure Portal

## Step 1 – Create VNet

1. Sign in to Azure Portal.
2. Search for **Virtual networks**.
3. Click **Create**.
4. Configure:
   - Resource Group: `rg-az104-dev-uks-001`
   - Name: `vnet-lb-uks-001`
   - Region: `UK South`
   - Address space: `10.40.0.0/16`
   - Subnet name: `subnet-web`
   - Subnet range: `10.40.1.0/24`
5. Add tags.
6. Click **Review + create**.
7. Click **Create**.

---

## Step 2 – Create NSG

1. Search for **Network security groups**.
2. Click **Create**.
3. Configure:
   - Name: `nsg-lb-web-001`
   - Resource Group: `rg-az104-dev-uks-001`
   - Region: `UK South`
4. Add tags.
5. Click **Create**.

---

## Step 3 – Add HTTP Rule

1. Open `nsg-lb-web-001`.
2. Go to **Inbound security rules**.
3. Click **Add**.
4. Configure:
   - Source: Any or Internet
   - Source port ranges: *
   - Destination: Any
   - Service: HTTP
   - Destination port: 80
   - Protocol: TCP
   - Action: Allow
   - Priority: 100
   - Name: Allow-HTTP
5. Click **Add**.

---

## Step 4 – Associate NSG to Subnet

1. Open `nsg-lb-web-001`.
2. Go to **Subnets**.
3. Click **Associate**.
4. Choose:
   - VNet: `vnet-lb-uks-001`
   - Subnet: `subnet-web`
5. Click **OK**.

---

## Step 5 – Create Public IP

1. Search for **Public IP addresses**.
2. Click **Create**.
3. Configure:
   - Name: `pip-lb-uks-001`
   - SKU: Standard
   - Assignment: Static
   - Region: UK South
4. Add tags.
5. Click **Create**.

---

## Step 6 – Create Load Balancer

1. Search for **Load balancers**.
2. Click **Create**.
3. Basics:
   - Resource Group: `rg-az104-dev-uks-001`
   - Name: `lb-web-uks-001`
   - Region: `UK South`
   - SKU: Standard
   - Type: Public
   - Tier: Regional
4. Frontend IP:
   - Name: `feip-web-001`
   - Public IP: `pip-lb-uks-001`
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
   - Probe: `probe-http-80`
8. Add tags.
9. Click **Review + create**.
10. Click **Create**.

---

## Step 7 – Create VM 1

1. Search for **Virtual machines**.
2. Click **Create** > **Azure virtual machine**.
3. Basics:
   - Name: `vm-lb-web-01`
   - Region: `UK South`
   - Image: Windows Server 2019 Datacenter
   - Size: Standard_B2s
   - Username: `azureuser`
   - Password: strong password
4. Networking:
   - VNet: `vnet-lb-uks-001`
   - Subnet: `subnet-web`
   - Public IP: None
   - NIC NSG: None or use subnet NSG
5. Add tags.
6. Create VM.

---

## Step 8 – Create VM 2

Repeat the VM creation process using:

```text
vm-lb-web-02
```

Use the same VNet and subnet.

---

## Step 9 – Add VMs to Backend Pool

1. Open `lb-web-uks-001`.
2. Go to **Backend pools**.
3. Open `bepool-web-001`.
4. Click **Add**.
5. Add:
   - `vm-lb-web-01`
   - `vm-lb-web-02`
6. Save.

---

## Step 10 – Install IIS Using Run Command

For each VM:

1. Open the VM.
2. Select **Run command**.
3. Choose **RunPowerShellScript**.

VM 1 script:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "<h1>AZ-104 Day 12</h1><p>Response from vm-lb-web-01</p>"
```

VM 2 script:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "<h1>AZ-104 Day 12</h1><p>Response from vm-lb-web-02</p>"
```

---

## Step 11 – Test Load Balancer

1. Open `pip-lb-uks-001`.
2. Copy the public IP address.
3. Browse to:

```text
http://<public-ip>
```

4. Refresh several times.

---

# Validation Checklist

| Check | Expected Result |
|---|---|
| Load Balancer exists | `lb-web-uks-001` |
| Public IP exists | `pip-lb-uks-001` |
| Backend pool exists | `bepool-web-001` |
| Backend pool members | 2 VM NICs |
| Probe exists | `probe-http-80` |
| Rule exists | `rule-http-80` |
| IIS installed | Both VMs respond |
| NSG allows HTTP | TCP 80 allowed |

---

# Troubleshooting

## Website does not load

Check:

- IIS installed on both VMs.
- NSG allows TCP 80.
- Backend pool contains both VMs.
- Health probe uses TCP 80.
- Load balancing rule maps 80 to 80.
- Public IP is correct.

## Only one VM responds

Check:

- Both VMs are in backend pool.
- IIS is installed on both VMs.
- Both VMs pass the health probe.
- VM-specific Windows Firewall allows HTTP.

## Backend instance unhealthy

Run on each VM:

```powershell
Get-Service W3SVC
```

If stopped:

```powershell
Start-Service W3SVC
```

## VM has no public IP

That is expected. Backend VMs should be private.

Use Run Command, Bastion, or private connectivity for administration.

---

# Enterprise Best Practices

- Use Standard Load Balancer for production.
- Use Availability Zones or Availability Sets for backend VMs.
- Keep backend VMs private.
- Use Azure Bastion for admin access.
- Use HTTPS for production workloads.
- Use Application Gateway/WAF for Layer 7 web security.
- Enable monitoring and alerts.
- Avoid exposing RDP/SSH publicly.

---

# Cleanup and Cost Control

Deallocate backend VMs:

```bash
az vm deallocate \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-lb-web-01

az vm deallocate \
  --resource-group rg-az104-dev-uks-001 \
  --name vm-lb-web-02
```

Delete lab resources if no longer needed:

```bash
az group delete \
  --name rg-az104-dev-uks-001 \
  --yes \
  --no-wait
```

Only delete the resource group if it does not contain resources you still need from other labs.
