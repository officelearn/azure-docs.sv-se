---
title: Distribuera resurser till hanterings grupp
description: Beskriver hur du distribuerar resurser i hanterings gruppens omfattning i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 23f86d7d0b7e1f882cf3fb74adc484e0fe47db87
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372433"
---
# <a name="create-resources-at-the-management-group-level"></a>Skapa resurser på hanterings grupps nivå

När din organisation är vuxen kan du distribuera en Azure Resource Manager-mall (ARM-mall) för att skapa resurser på hanterings grupps nivå. Du kan till exempel behöva definiera och tilldela [principer](../../governance/policy/overview.md) eller [Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) för en hanterings grupp. Med mallar för hanterings grupps nivåer kan du tillämpa principer och tilldela roller på hanterings grupps nivån.

## <a name="supported-resources"></a>Resurser som stöds

Alla resurs typer kan inte distribueras till hanterings grupps nivån. I det här avsnittet listas vilka resurs typer som stöds.

För Azure-ritningar använder du:

* [artefakter](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [modeller](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [5](/azure/templates/microsoft.blueprint/blueprints/versions)

Använd följande för Azure-principer:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [reparationer](/azure/templates/microsoft.policyinsights/remediations)

För rollbaserad åtkomst kontroll i Azure (Azure RBAC) använder du:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

För kapslade mallar som distribuerar till prenumerationer eller resurs grupper använder du:

* [distributioner](/azure/templates/microsoft.resources/deployments)

Använd följande för att hantera resurser:

* [taggen](/azure/templates/microsoft.resources/tags)

## <a name="schema"></a>Schema

Schemat som används för distributioner av hanterings grupper skiljer sig från schemat för distributioner av resurs grupper.

För mallar använder du:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Schemat för en parameter fil är detsamma för alla distributions omfång. För parameter-filer använder du:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-scopes"></a>Distributions omfång

När du distribuerar till en hanterings grupp kan du rikta in den angivna hanterings gruppen i distributions kommandot, eller så kan du välja en annan hanterings grupp i klienten.

De resurser som definieras i avsnittet resurser i mallen tillämpas på hanterings gruppen från distributions kommandot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

Om du vill rikta en annan hanterings grupp lägger du till en kapslad distribution och anger `scope` egenskapen. Ange `scope` ett värde i formatet för egenskapen `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,22":::

Du kan också rikta prenumerationer eller resurs grupper i en hanterings grupp. Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

Om du vill rikta en prenumeration inom hanterings gruppen använder du en kapslad distribution och `subscriptionId` egenskapen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="10,18":::

Om du vill rikta en resurs grupp i den prenumerationen lägger du till en annan kapslad distribution och `resourceGroup` egenskapen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="10,21,25":::

Om du vill använda en distribution av hanterings grupper för att skapa en resurs grupp i en prenumeration och distribuera ett lagrings konto till den resurs gruppen, se [distribuera till prenumeration och resurs grupp](#deploy-to-subscription-and-resource-group).

## <a name="deployment-commands"></a>Distributions kommandon

Kommandona för distributioner av hanterings grupper skiljer sig från kommandona för resurs grupps distributioner.

För Azure CLI använder du [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

För Azure PowerShell använder du [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

För REST API använder du [distributioner – skapa i hanterings gruppens omfång](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Distributions plats och namn

För distributioner på hanterings grupp nivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributions platsen anger var distributions data ska lagras.

Du kan ange ett namn för distributionen eller använda standard distributions namnet. Standard namnet är namnet på mallfilen. Om du till exempel distribuerar en mall som heter **azuredeploy.jspå** skapas ett standard distributions namn för **azuredeploy**.

För varje distributions namn är platsen oföränderlig. Du kan inte skapa en distribution på en plats om det finns en befintlig distribution med samma namn på en annan plats. Om du får fel koden `InvalidDeploymentLocation` använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="use-template-functions"></a>Använda mall funktioner

För distributioner av hanterings grupper finns det några viktiga saker att tänka på när du använder mall-funktioner:

* Funktionen [resourceGroup ()](template-functions-resource.md#resourcegroup) stöds **inte** .
* Funktionen [Subscription ()](template-functions-resource.md#subscription) stöds **inte** .
* Funktionerna [Reference ()](template-functions-resource.md#reference) och [List ()](template-functions-resource.md#list) stöds.
* Använd inte funktionen [resourceId ()](template-functions-resource.md#resourceid) för resurser som distribueras till hanterings gruppen.

  Använd i stället funktionen [extensionResourceId ()](template-functions-resource.md#extensionresourceid) för resurser som implementeras som tillägg för hanterings gruppen. Anpassade princip definitioner som distribueras till hanterings gruppen är tillägg för hanterings gruppen.

  Använd följande för att hämta resurs-ID för en anpassad princip definition på hanterings grupps nivå:
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

  Använd funktionen [tenantResourceId](template-functions-resource.md#tenantresourceid) för klient resurser som är tillgängliga i hanterings gruppen. Inbyggda princip definitioner är klient nivå resurser.

  Om du vill hämta resurs-ID för en inbyggd princip definition använder du:
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="azure-policy"></a>Azure Policy

I följande exempel visas hur du [definierar](../../governance/policy/concepts/definition-structure.md) en princip på hanterings grupps nivån och tilldelar den.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Distribuera till prenumeration och resurs grupp

Från en distribution på hanterings grupp nivå kan du rikta en prenumeration i hanterings gruppen. I följande exempel skapas en resurs grupp i en prenumeration och ett lagrings konto distribueras till den resurs gruppen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du tilldelar roller finns i [lägga till Azure-roll tilldelningar med hjälp av Azure Resource Manager mallar](../../role-based-access-control/role-assignments-template.md).
* Ett exempel på hur du distribuerar inställningar för arbets ytan för Azure Security Center finns [deployASCwithWorkspaceSettings.jspå](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Du kan också distribuera mallar på [prenumerations nivå](deploy-to-subscription.md) och [klient nivå](deploy-to-tenant.md).
