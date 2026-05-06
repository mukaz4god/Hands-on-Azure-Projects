# Day 04 – Setup Guide (CLI + Portal)

## CLI Method

Create SSH rule:
az network nsg rule create --resource-group rg-az104-dev-uks-001 --nsg-name nsg-rdp-001 --name Allow-SSH --protocol Tcp --priority 1020 --destination-port-range 22 --access Allow

Create VM:
az vm create --resource-group rg-az104-dev-uks-001 --name vm-linux-001 --image Ubuntu2204 --admin-username azureuser --generate-ssh-keys

SSH:
ssh azureuser@<public-ip>

## Portal Method

1. Go to Virtual Machines → Create
2. Select Ubuntu image
3. Choose SSH authentication
4. Add inbound rule port 22
5. Connect via SSH

## Validation
- SSH works
- NSG rule exists
