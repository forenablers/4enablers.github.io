---
categories: Azure, governance, compliance, policy, security, devops, architecture, operational excellence
title: Azure Governance - Policies for Cloud Excellence
date: 2023-01-02T00:00:00Z
author: Borys Generalov
---

In today's fast-changing digital world, robust governance in the Cloud ecosystem is very important for operational efficiency, security, and compliance. Effective governance helps to keep things running smoothly. This is crucial in cloud environments where elasticity (the ability to add/remove resources depending on how much they are needed) is the key.

Furthermore, having good governance is important for making sure things are safe. As computer attacks get smarter, having a strong governance framework helps to identify and mitigate security risks before they cause problems. Azure offers their customers efficient tools to enforce consistent security policies and keep their cloud footprint safe. This makes sure that no one can get into their data and applications without permission.

Lastly, in an era of strengthened regulations like GDPR and HIPAA, Azure's governance is instrumental in ensuring compliance. This helps organizations adhere to legal and regulatory standards, essential for lawful cloud operations.

## Governance with Azure Policies

Governance within Azure is primarily achieved through the application of Azure Policies, which are rules that define the standards and expectations for cloud resources. These policies act as a set of rules that guide the configuration and management of resources within the Azure cloud. For instance, they can enforce the use of firewalls, restrict resources from accepting traffic from the public internet, and ensure that data storage complies with regulatory requirements. Azure Policies automate and enforce these standards, allowing for governance at scale and facilitating a uniform security posture. This sets the stage for an organized and secure cloud infrastructure, which is essential for achieving optimal cloud performance and reliability.

Enforcing governance in Azure is a structured process that begins with defining the policy. Once defined, the next step is to assign the policy to the desired scope through the Azure portal, Azure CLI, or scripts. After a policy is in place, the focus shifts to ensuring that the resources adhere to the assigned policies. Azure facilitates this by offering continuous compliance data. Azure maintains continuous compliance through an automated assessment engine that regularly checks resource adherence to policies. Any changes in resources or policies prompt an immediate re-evaluation. The results are accessible via the Azure portal, which displays the current compliance status through intuitive dashboards. Additionally, Azure can send alerts or integrate with external services to notify administrators of any compliance status changes, allowing for prompt policy adjustments and consistent adherence to governance standards.

## Understanding Azure Policy Components

Azure Policy consists of critical components that work in unison to enforce governance across your cloud environment. Firstly, `Policy Definitions` are the core rules that outline the conditions under which specific policies apply. These can be either built-in or custom-defined, providing flexibility for tailored governance requirements.

Secondly, `Policy Assignments` involve applying a policy definition to a specific scope, such as a subscription or resource group. This step is crucial as it ensures the defined policies are actively enforced where necessary. Lastly, `Policy Sets` or `Initiatives` group multiple policy definitions, simplifying management and assignment. This aggregation is especially useful for complex governance requirements involving multiple policies.

## Practical example with Azure Bicep

To demonstrate Azure policy-driven governance in practice, let's examine a real-world example using Azure Bicep, a language designed for efficient Azure resource deployment, that enables you to define your infrastructure as code in a clear and structured manner. 

In the following section, you will see how to create a `policy definition` that limits App Service SKUs and enhances security by denying unsecured HTTP and disabling FTP. Below is the Bicep code that outlines this policy definition:

```bicep
var appServiceSku = 'Standard'
var allowedSKUs = ['Free', 'Shared', 'Basic', 'Standard', 'Premium', 'Isolated']

resource appServicePolicy 'Microsoft.Authorization/policyDefinitions@2021-06-01' = {
  name: 'limit-app-service-sku-and-enhance-security'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    displayName: 'Enhance Security and Limit App Service SKUs'
    description: 'This policy limits the SKUs of App Services and enforces secure connections.'
    policyRule: {
      if: {
        allOf: [
          {
            field: 'Microsoft.Web/serverFarms/sku.name'
            notIn: allowedSKUs
          },
          {
            field: 'Microsoft.Web/sites/httpsOnly'
            equals: false
          },
          {
            field: 'Microsoft.Web/sites/ftpState'
            notEquals: 'Disabled'
          }
        ]
      }
      then: {
        effect: 'deny'
      }
    }
  }
}
```

When a user attempts to deploy an Azure App Service that does not comply with the specified policy, for instance with HTTP enabled, the deployment will fail due to Azure Policy's rules violation. The policy's 'deny' effect blocks any non-compliant resource creation or updates. The user receives an error message indicating a policy violation, specifically related to the HTTPS-only requirement. Details of this failed deployment and the specific policy violation can be found in the Azure Activity Log and under the Policy Compliance section in the Azure portal.

Once you've defined your Azure policy, the next step is to organize it within an `initiative` or `policy set`. Initiatives group related policies, making them easier to manage and apply consistently. By using an initiative, you ensure that all the related governance rules are enforced together, enhancing the security and compliance of your Azure resources. Here's how you can structure an initiative in Azure Bicep:

```bicep
var initiativeName = 'app-service-governance-initiative'

resource policySet 'Microsoft.Authorization/policySetDefinitions@2021-06-01' = {
  name: initiativeName
  properties: {
    displayName: 'App Service Governance Initiative'
    description: 'Grouping policies related to App Service governance'
    policyDefinitions: [
      {
        policyDefinitionId: policyDefinition.id
      }
      // Additional policy definitions can be included here
    ]
  }
}
```

The final step in policy implementation is the `policy assignment`. This is where you apply your initiative to your Azure environment, that is you need to assign it to a specific scope: this could be a subscription, resource group, or a management group in Azure. Assigning your initiative to a scope activates the policies within that environmen, ensuring your setup adheres to your governance standards. Here's a simple Bicep code snippet for creating a policy assignment:

```bicep
resource policyAssignment 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: 'assignment-${initiativeName}'
  properties: {
    scope: '/subscriptions/<your-subscription-id>'
    policyDefinitionId: policySet.id
    description: 'Assignment of the App Service Governance Initiative'
    displayName: 'App Service Governance Initiative Assignment'
  }
}
```

## Organizing Azure Policies

So, how do you organize the policy guardrails? You define policies and group them into initiatives, and there are several ways to structure these initiatives effectively. Microsoft provides valuable examples of organized built-in policies and initiatives that can guide you. You can explore these examples and understand how they are organized by visiting [Azure Built-in policies](https://learn.microsoft.com/en-us/azure/governance/policy/samples/built-in-policies) in the official Microsoft Azure documentation.

ANother approach is to group policies based on resource types, security levels, or other relevant criteria that align with your organization's specific needs. This ensures a coherent and manageable approach to policy governance and compliance in your Azure environment. However, avoid organizing initiatives per application or service, as it can lead to policy fragmentation and complexity. Alternatively, you can group policies based on security levels, such as critical or non-critical. This helps prioritize and enforce security controls according to resource importance, maintaining flexibility while ensuring compliance.

## Deploying policies

When it comes to deploying Azure policies, you can approach it much like deploying any other Azure resources, using Bicep. However, as your Azure environment and the number of policies grow, managing them at scale can become challenging. Deploying and managing Azure policies at scale, particularly in large organizations, poses significant challenges. Understanding which policies are assigned to which scopes and maintaining consistency can become overwhelming. To address this complexity, enhanced tooling is essential.

One such tool is [AzOps](https://github.com/Azure/AzOps), which follows a GitOps-style approach for Azure policy management. AzOps, an open-source that integrates with Azure DevOps Continuous Devlivery practices, by treating everything as a resource, managed declaratively to define the target state. In a nutshell it's a PowerShell module that deploys Resource Templates and Bicep files across Azure scopes using Git repositories.

In AzOps, Azure resources and policies are organized within a Git repository, representing various Azure scope levels like management groups, subscriptions, and resource groups. You can push templates to enforce configurations and pull composite templates to maintain desired states. Below is a simplified example of a Git repository file structure for managing Azure policies with AzOps:

```bash
root
└── 
    └── subscription-1
        ├── microsoft.authorization_policyassignments-securitycenterbuiltin.json
    └── subscription-2
        ├── microsoft.authorization_policyassignments-dataprotectionsecuritycenter.json
        ├── microsoft.authorization_policyassignments-securitycenterbuiltin.json
    ├── microsoft.authorization_policydefinitions-append-appservice-httpsonly.parameters.json
    ├── microsoft.authorization_policydefinitions-append-kv-softdelete.parameters.json
    ├── microsoft.authorization_policydefinitions-deny-appgw-without-waf.parameters.json
    ...
    ├── microsoft.authorization_policysetdefinitions-deploy-sql-security.parameters.json
    └── microsoft.authorization_policysetdefinitions-enforce-encryption-cmk.parameters.json
```

This approach ensures consistency, scalability, and governance in your Azure environment while providing version-controlled policy and resource history. Explore [AzOps](https://github.com/Azure/AzOps) and [AzOps Accelerator](https://github.com/Azure/AzOps-Accelerator) in its GitHub repository for further details.

## Summary

This article covered the essentials of Azure policy-driven governance. You've gained an understanding of how to create policy definitions and initiatives tailored to your organization's specific requirements. Additionally, you've explored the way for managing policies at scale with a GitOps-style approach.

To continue your journey in Azure policy-driven governance and gain deeper insights, here are some valuable resources:

* [Microsoft Cloud Adoption Framework](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/govern/)
* [Azure Policy](https://learn.microsoft.com/en-us/azure/governance/policy/overview)
* [AzOps Accelerator](https://github.com/Azure/AzOps-Accelerator)