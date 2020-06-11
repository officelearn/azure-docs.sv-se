---
title: Distribuera resurser till prenumerationen
description: Beskriver hur du skapar en resurs grupp i en Azure Resource Manager-mall. Det visar också hur du distribuerar resurser i Azures prenumerations omfång.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 17ae335fa40a71ed266211150df382404efbf61b
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84673980"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Skapa resurs grupper och resurser på prenumerations nivå

För att förenkla hanteringen av resurser kan du distribuera resurser på nivån i din Azure-prenumeration. Du kan till exempel distribuera [principer](../../governance/policy/overview.md) och [rollbaserade åtkomst kontroller](../../role-based-access-control/overview.md) till din prenumeration, och dessa resurser tillämpas i din prenumeration. Du kan också skapa resurs grupper och distribuera resurser till dessa resurs grupper.

> [!NOTE]
> Du kan distribuera till 800 olika resurs grupper i en distribution på prenumerations nivå.

Om du vill distribuera mallar på prenumerations nivån använder du Azure CLI, PowerShell eller REST API. Azure Portal har inte stöd för distribution på prenumerations nivån.

## <a name="supported-resources"></a>Resurser som stöds

Du kan distribuera följande resurs typer på prenumerations nivån:

* [modeller](/azure/templates/microsoft.blueprint/blueprints)
* [budget](/azure/templates/microsoft.consumption/budgets)
* [distributioner](/azure/templates/microsoft.resources/deployments) – för kapslade mallar som distribueras till resurs grupper.
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [reparationer](/azure/templates/microsoft.policyinsights/2019-07-01/remediations)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)
* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [taggen](/azure/templates/microsoft.resources/tags)
* [workspacesettings](/azure/templates/microsoft.security/workspacesettings)

### <a name="schema"></a>Schema

Schemat som används för distributioner på prenumerations nivå skiljer sig från schemat för distributioner av resurs grupper.

För mallar använder du:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Schemat för en parameter fil är detsamma för alla distributions omfång. För parameter-filer använder du:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Distributions kommandon

Kommandona för distributioner på prenumerations nivå skiljer sig från kommandona för resurs grupp distributioner.

För Azure CLI använder du [AZ Deployment sub Create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create). I följande exempel distribueras en mall för att skapa en resurs grupp:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

För kommandot PowerShell-distribution använder du [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) eller **New-AzSubscriptionDeployment**. I följande exempel distribueras en mall för att skapa en resurs grupp:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

För REST API använder du [distributioner – skapa vid prenumerations omfång](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Distributions plats och namn

För distributioner på prenumerations nivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributions platsen anger var distributions data ska lagras.

Du kan ange ett namn för distributionen eller använda standard distributions namnet. Standard namnet är namnet på mallfilen. Om du till exempel distribuerar en mall som heter **azuredeploy.jspå** skapas ett standard distributions namn för **azuredeploy**.

För varje distributions namn är platsen oföränderlig. Du kan inte skapa en distribution på en plats om det finns en befintlig distribution med samma namn på en annan plats. Om du får fel koden `InvalidDeploymentLocation` använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="use-template-functions"></a>Använda mall funktioner

För distributioner på prenumerations nivå finns det några viktiga överväganden när du använder mallarna:

* Funktionen [resourceGroup ()](template-functions-resource.md#resourcegroup) stöds **inte** .
* Funktionerna [Reference ()](template-functions-resource.md#reference) och [List ()](template-functions-resource.md#list) stöds.
* Använd funktionen [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) för att hämta resurs-ID för resurser som distribueras på prenumerations nivå.

  Om du till exempel vill hämta resurs-ID för en princip definition använder du:

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  Det returnerade resurs-ID: t har följande format:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Skapa resurs grupper

Skapa en resurs grupp i en Azure Resource Manager mall genom att definiera en resurs för [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) med ett namn och en plats för resurs gruppen. Du kan skapa en resurs grupp och distribuera resurser till den resurs gruppen i samma mall.

Följande mall skapar en tom resurs grupp.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2019-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Använd [Kopiera-elementet](copy-resources.md) med resurs grupper för att skapa fler än en resurs grupp.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2019-10-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Information om resurs upprepning finns i [distribuera fler än en instans av en resurs i Azure Resource Manager mallar](./copy-resources.md)och [Självstudier: skapa flera resurs instanser med Resource Manager-mallar](./template-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Resurs grupp och resurser

Använd en kapslad mall för att skapa resurs gruppen och distribuera resurser till den. Den kapslade mallen definierar de resurser som ska distribueras till resurs gruppen. Ange att den kapslade mallen är beroende av resurs gruppen för att kontrol lera att resurs gruppen finns innan du distribuerar resurserna. Du kan distribuera till upp till 800 resurs grupper.

I följande exempel skapas en resurs grupp och ett lagrings konto distribueras till resurs gruppen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2019-10-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Tilldela princip definition

I följande exempel tilldelas en befintlig princip definition till prenumerationen. Om princip definitionen tar parametrar, anger du dem som ett objekt. Om princip definitionen inte tar parametrar använder du det tomma standard objektet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Om du vill distribuera den här mallen med Azure CLI använder du:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Använd följande om du vill distribuera den här mallen med PowerShell:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>Skapa och tilldela princip definitioner

Du kan [definiera](../../governance/policy/concepts/definition-structure.md) och tilldela en princip definition i samma mall.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Använd följande CLI-kommando om du vill skapa en princip definition i din prenumeration och tilldela den till prenumerationen:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Använd följande om du vill distribuera den här mallen med PowerShell:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprint

### <a name="create-blueprint-definition"></a>Skapa skiss definition

Du kan [skapa](../../governance/blueprints/tutorials/create-from-sample.md) en skiss definition från en mall.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Använd följande CLI-kommando om du vill skapa en skiss definition i din prenumeration:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Använd följande om du vill distribuera den här mallen med PowerShell:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="template-samples"></a>Exempel på mallar

* [Skapa en resurs grupp, lås den och ge behörighet till den](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-rg-lock-role-assignment).
* [Skapa en resurs grupp, en princip och en princip tilldelning](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Nästa steg

* Information om hur du tilldelar roller finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure Resource Manager mallar](../../role-based-access-control/role-assignments-template.md).
* Ett exempel på hur du distribuerar inställningar för arbets ytan för Azure Security Center finns [deployASCwithWorkspaceSettings.jspå](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Du hittar exempel på mallar på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments).
* Du kan också distribuera mallar på [hanterings grupp nivå](deploy-to-management-group.md) och [klient nivå](deploy-to-tenant.md).
