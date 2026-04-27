# AZ-104 Hands-On Project – Day 01: Azure Fundamentals Lab

## Project Overview

This is Day 01 of my 30-day scenario-based AZ-104 Azure Administrator hands-on project series.

The goal of this lab is to create the basic Azure governance foundation required before deploying real workloads.

## Scenario

A company is starting its Azure journey and needs a clean development environment. As the Azure Administrator, I created a resource group, applied governance tags, and configured RBAC access at the correct scope.

## What I Built

- Azure resource group
- Standard governance tags
- RBAC role assignment
- CLI validation workflow

## Technologies Used

- Microsoft Azure
- Azure Resource Manager
- Azure Portal
- Azure CLI
- Azure RBAC
- Azure Tags

## Architecture / Governance Scope

```text
Azure Subscription
│
└── Resource Group: rg-az104-dev-uks-001
    ├── Tags
    │   ├── env=dev
    │   ├── owner=<your-name>
    │   ├── project=az104-practice
    │   └── costCenter=learning
    │
    └── RBAC
        └── Owner assigned to lab user
```

## Files in This Folder

| File | Purpose |
|---|---|
| `Case Study.md` | Scenario, business requirements, AZ-104 mapping |
| `Setup Guide.md` | Step-by-step implementation guide |
| `Interview.md` | Interview questions and answers |
| `README.md` | Project overview for GitHub |

## Key Learning Outcomes

By completing this lab, I practised:

- Creating and managing resource groups
- Applying Azure tags
- Assigning RBAC roles
- Validating configuration with Azure CLI
- Understanding Azure governance foundations

## Azure Best Practice Notes

- Use resource groups to manage resources that share the same lifecycle.
- Apply tags consistently for ownership, environment, and cost tracking.
- Avoid storing sensitive information in tags.
- Assign RBAC at the lowest practical scope.

## Evidence

Add screenshots to:

```text
/images/day01/
```

Recommended screenshots:

- Resource group overview
- Tags
- IAM role assignment
- CLI validation output

## Cleanup

If continuing to Day 2, keep the resource group.

If deleting:

```bash
az group delete --name rg-az104-dev-uks-001 --yes --no-wait
```

## Official Azure References

- Azure Resource Manager overview: https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/overview
- Manage resource groups in Azure Portal: https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal
- Use tags to organise Azure resources: https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/tag-resources
- Azure RBAC overview: https://learn.microsoft.com/en-us/azure/role-based-access-control/overview
- Azure Well-Architected Framework: https://learn.microsoft.com/en-us/azure/well-architected/
