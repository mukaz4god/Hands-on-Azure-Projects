# Day 01 – Interview Questions and Answers

## 1. What is an Azure subscription?

An Azure subscription is a billing and management boundary for Azure resources. It is linked to a Microsoft Entra ID tenant and can contain resource groups and resources.

## 2. What is a resource group?

A resource group is a logical container that holds related Azure resources for a solution. It helps you manage resources together based on lifecycle, ownership, or environment.

## 3. Why should resources with the same lifecycle be placed in the same resource group?

Because resource groups make it easier to deploy, update, monitor, and delete resources together. If resources are part of the same application or environment, grouping them reduces operational mistakes.

## 4. What are Azure tags?

Tags are key-value metadata used to organise Azure resources. They are commonly used for cost tracking, ownership, environment classification, and governance.

## 5. Should sensitive information be stored in tags?

No. Tags are stored as plain text and can appear in cost reports, deployment history, logs, and exported templates.

## 6. What is RBAC?

Azure role-based access control is the authorisation system used to manage who has access to Azure resources, what they can do, and at what scope.

## 7. What are the common RBAC scopes?

RBAC can be assigned at:

- Management group
- Subscription
- Resource group
- Resource

## 8. Why is assigning Owner at resource group scope better than subscription scope for this lab?

It follows least privilege. You only grant full control over the specific lab resource group instead of the entire subscription.

## 9. What is the difference between Owner and Contributor?

`Owner` can manage resources and assign access to others. `Contributor` can manage resources but cannot grant access to others.

## 10. How does this lab relate to the Azure Well-Architected Framework?

It supports security through RBAC, cost optimisation through tags, and operational excellence through clear resource organisation.

## 11. How would you explain this project in an interview?

I created a governance foundation in Azure by deploying a dedicated resource group, applying standard tags for ownership and cost tracking, and configuring RBAC at the resource group scope. I validated the deployment through both Azure Portal and Azure CLI, following least-privilege and operational best practices.
