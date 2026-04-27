# Day 01 – Azure Fundamentals Lab: Governance Foundation

## Scenario

You have joined a small cloud team as the Azure Administrator. The organisation wants to start deploying workloads into Azure, but before any virtual machines, storage accounts, or applications are created, the platform needs a clean governance foundation.

Your task is to prepare the first Azure environment by creating a resource group, applying standard tags, and assigning role-based access control (RBAC). This lab proves that you understand the Azure resource hierarchy, governance basics, and access control — all core AZ-104 skills.

## Business Requirement

The company needs a dedicated development resource group where future Azure resources can be deployed and managed together.

## Technical Requirements

Create or confirm access to an Azure subscription.

Create a resource group for development workloads.

Apply standard tags:

- `env=dev`
- `owner=<your-name-or-github-username>`
- `project=az104-practice`
- `costCenter=learning`

Assign yourself the `Owner` role at the resource group scope.

Validate the configuration using Azure Portal and Azure CLI.

Clean up only if you are not continuing to Day 2.

## AZ-104 Skills Covered

- Manage Azure subscriptions and governance
- Configure resource groups
- Configure tags
- Configure Azure role-based access control
- Understand Azure Resource Manager scope hierarchy

## Azure Well-Architected Framework Alignment

| Pillar | How this lab supports it |
|---|---|
| Security | RBAC ensures access is granted at the correct scope. |
| Cost Optimization | Tags support ownership and cost tracking. |
| Operational Excellence | Resource groups provide a clean management boundary. |
| Reliability | Resources with the same lifecycle are grouped together. |
| Performance Efficiency | Not directly tested on Day 1, but this foundation supports future scalable deployments. |

## Success Criteria

You should be able to show:

- A resource group created in Azure.
- Tags applied correctly.
- Your account assigned the `Owner` role at resource group scope.
- CLI validation output confirming the resource group and tags.
