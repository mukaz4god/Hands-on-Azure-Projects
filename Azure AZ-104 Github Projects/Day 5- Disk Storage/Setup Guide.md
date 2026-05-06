# Day 05 – Setup Guide

## CLI

Create disk:

az disk create --resource-group rg-az104-dev-uks-001 --name disk-linux-data-001 --size-gb 32 --sku StandardSSD_LRS

Attach disk:

az vm disk attach --resource-group rg-az104-dev-uks-001 --vm-name vm-linux-001 --name disk-linux-data-001

SSH into VM and configure filesystem.

## Portal

1. Go to Disks → Create
2. Attach disk to VM
3. SSH into VM
4. Configure mount
5. Create snapshot
