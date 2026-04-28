# Day 01 – Setup Guide: Azure Fundamentals Lab

## Objective

Build the governance foundation for your AZ-104 practice environment by creating a resource group, applying tags, and configuring RBAC.

## Prerequisites

- Azure account
- Active Azure subscription
- Azure Portal access
- Azure CLI installed locally, or Azure Cloud Shell
- Basic understanding of subscriptions, resource groups, tags, and RBAC

## Naming Convention

Use a consistent naming format:

```text
rg-az104-dev-uks-001
```

Recommended format:

```text
rg-<workload>-<environment>-<region>-<number>
```

Example:

```text
rg-az104-dev-uks-001
```

## Step 1 – Sign in to Azure

### Option A: Azure Portal

Go to Azure Portal and sign in.

### Option B: Azure CLI

```bash
az login
```

Confirm your active subscription:

```bash
az account show --output table
```

If you have multiple subscriptions, select the correct one:

```bash
az account set --subscription "<subscription-id-or-name>"
```

## Step 2 – Create the Resource Group

Choose a region close to you. For the UK, use:

```text
uksouth
```

Create the resource group:

```bash
az group create \
  --name rg-az104-dev-uks-001 \
  --location uksouth
```

## Step 3 – Apply Tags

Apply governance tags:

```bash
az group update \
  --name rg-az104-dev-uks-001 \
  --set tags.env=dev tags.owner="<your-name>" tags.project=az104-practice tags.costCenter=learning
```

Validate tags:

```bash
az group show \
  --name rg-az104-dev-uks-001 \
  --query tags \
  --output table
```

## Step 4 – Assign RBAC

Get your signed-in user object ID:

```bash
az ad signed-in-user show --query id --output tsv
```

Assign yourself the `Owner` role at the resource group scope:

```bash
USER_ID=$(az ad signed-in-user show --query id --output tsv)

SUBSCRIPTION_ID=$(az account show --query id --output tsv)

az role assignment create \
  --assignee $USER_ID \
  --role "Owner" \
  --scope "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/rg-az104-dev-uks-001"
```

## Step 5 – Validate RBAC Assignment

```bash
az role assignment list \
  --assignee $USER_ID \
  --scope "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/rg-az104-dev-uks-001" \
  --output table
```

## Step 6 – Portal Validation

In Azure Portal:

1. Go to **Resource Groups**.
2. Open `rg-az104-dev-uks-001`.
3. Confirm the tags exist.
4. Go to **Access control (IAM)**.
5. Confirm your account has the `Owner` role.

## Step 7 – Evidence for GitHub

Screenshots of:

- Resource group overview
 <img width="932" height="391" alt="image" src="https://github.com/user-attachments/assets/783a540f-580d-4276-a663-54e2f08ba79b" />
<br/>
- Tags page
 <img width="932" height="300" alt="image" src="https://github.com/user-attachments/assets/e6363da6-3c2e-409a-8428-f0546fccf1e6" />
<br/>
- IAM role assignment page
<img width="889" height="318" alt="image" src="https://github.com/user-attachments/assets/44eaf2f6-d096-4b8c-a78d-f4f921d34dfd" />

## Step 8 – Cleanup

Do not delete the resource group if you will continue to Day 2.

If you want to delete it:

```bash
az group delete \
  --name rg-az104-dev-uks-001 \
  --yes \
  --no-wait
```

## Common Issues

### Issue: You cannot assign Owner role

You may not have permission at the subscription level. Use an account with `Owner` or `User Access Administrator` permissions.

### Issue: Azure CLI cannot find your user

Make sure you are signed in:

```bash
az login
```

### Issue: Region name fails

Use Azure CLI to list valid regions:

```bash
az account list-locations --output table
```
