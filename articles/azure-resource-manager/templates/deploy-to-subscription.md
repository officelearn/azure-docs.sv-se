---
title: Distribuera resurser till prenumeration
description: Beskriver hur du skapar en resursgrupp i en Azure Resource Manager-mall. Den visar också hur du distribuerar resurser i Azure-prenumerationsomfattningen.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 6bec29a07653ff5ad7d1e2f8317246049e127c8c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604995"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Skapa resursgrupper och resurser på prenumerationsnivå

Om du vill förenkla hanteringen av resurser i din [Azure-prenumeration](../../role-based-access-control/overview.md) kan du definiera och tilldela [principer](../../governance/policy/overview.md) eller rollbaserade åtkomstkontroller över prenumerationen. Med mallar på prenumerationsnivå tillämpar du principer och tilldelar roller i prenumerationen. Du kan också skapa resursgrupper och distribuera resurser.

Om du vill distribuera mallar på prenumerationsnivå använder du Azure CLI, PowerShell eller REST API. Azure-portalen stöder inte distribution på prenumerationsnivå.

## <a name="supported-resources"></a>Resurser som stöds

Du kan distribuera följande resurstyper på prenumerationsnivå:

* [Budgetar](/azure/templates/microsoft.consumption/budgets)
* [distributioner](/azure/templates/microsoft.resources/deployments) - för kapslade mallar som distribueras till resursgrupper.
* [händelseAbonnemang](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitioner](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [avhjälpande åtgärder](/azure/templates/microsoft.policyinsights/2019-07-01/remediations)
* [resursGrupper](/azure/templates/microsoft.resources/resourcegroups)
* [rollTilldelningar](/azure/templates/microsoft.authorization/roleassignments)
* [rollDefinitioner](/azure/templates/microsoft.authorization/roledefinitions)
* [scopeAstilldelningar](/azure/templates/microsoft.managednetwork/scopeassignments)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Taggar](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>Schema

Schemat som du använder för distributioner på prenumerationsnivå skiljer sig från schemat för resursgruppsdistributioner.

För mallar använder du:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Schemat för en parameterfil är detsamma för alla distributionsomfattningar. Använd för parameterfiler:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Distributionskommandon

Kommandona för distributioner på prenumerationsnivå skiljer sig från kommandona för resursgruppsdistributioner.

För Azure CLI använder du [az deployment sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create). I följande exempel distribueras en mall för att skapa en resursgrupp:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Använd [Kommandot Ny-AzDeployment](/powershell/module/az.resources/new-azdeployment) eller **New-AzSubscriptionDeployment**för powershell-distribution. I följande exempel distribueras en mall för att skapa en resursgrupp:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

För REST API använder du [Distributioner - Skapa vid prenumerationsomfattning](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Distributionsplats och namn

För prenumerationsnivådistributioner måste du ange en plats för distributionen. Platsen för distributionen är skild från platsen för de resurser som du distribuerar. Distributionsplatsen anger var distributionsdata ska lagras.

Du kan ange ett namn för distributionen eller använda standarddistributionsnamnet. Standardnamnet är namnet på mallfilen. Om du till exempel distribuerar en mall med namnet **azuredeploy.json** skapas ett standarddistributionsnamn för **azuredeploy**.

För varje distributionsnamn är platsen oföränderlig. Du kan inte skapa en distribution på en plats när det finns en befintlig distribution med samma namn på en annan plats. Om du får `InvalidDeploymentLocation`felkoden använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="use-template-functions"></a>Använda mallfunktioner

För distributioner på prenumerationsnivå finns det några viktiga överväganden när du använder mallfunktioner:

* Funktionen [resourceGroup()](template-functions-resource.md#resourcegroup) stöds **inte.**
* Funktionerna [reference()](template-functions-resource.md#reference) och [list()](template-functions-resource.md#list) stöds.
* Använd funktionen [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) för att hämta resurs-ID för resurser som distribueras på prenumerationsnivå.

  Om du till exempel vill hämta resurs-ID:t för en principdefinition använder du:
  
  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  Det returnerade resurs-ID:et har följande format:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Skapa resursgrupper

Om du vill skapa en resursgrupp i en Azure Resource Manager-mall definierar du en [Microsoft.Resources/resourceGroups-resurs](/azure/templates/microsoft.resources/allversions) med ett namn och en plats för resursgruppen. Du kan skapa en resursgrupp och distribuera resurser till resursgruppen i samma mall.

I följande mall skapas en tom resursgrupp.

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
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Använd [kopieringselementet](copy-resources.md) med resursgrupper för att skapa mer än en resursgrupp.

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
      "apiVersion": "2018-05-01",
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

Information om resursiteriteration finns [i Distribuera mer än en instans av en resurs i Azure Resource Manager-mallar](./copy-resources.md)och [självstudiekurs: Skapa flera resursinstanser med Resource Manager-mallar](./template-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Resursgrupp och resurser

Om du vill skapa resursgruppen och distribuera resurser till den använder du en kapslad mall. Den kapslade mallen definierar de resurser som ska distribueras till resursgruppen. Ange den kapslade mallen som beroende av resursgruppen för att se till att resursgruppen finns innan resurserna distribueras.

I följande exempel skapas en resursgrupp och ett lagringskonto distribueras till resursgruppen.

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
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2017-10-01",
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

## <a name="create-policies"></a>Skapa principer

### <a name="assign-policy"></a>Tilldela princip

I följande exempel tilldelas en befintlig principdefinition till prenumerationen. Om principen tar parametrar anger du dem som ett objekt. Om principen inte tar parametrar använder du standardobjektet för tomma objekt.

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
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Om du vill distribuera den här mallen med PowerShell använder du:

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

### <a name="define-and-assign-policy"></a>Definiera och tilldela princip

Du kan [definiera](../../governance/policy/concepts/definition-structure.md) och tilldela en princip i samma mall.

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

Om du vill skapa principdefinitionen i prenumerationen och använda den på prenumerationen använder du följande CLI-kommando:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="template-samples"></a>Exempel på mallar

* [Skapa en resursgrupp, lås den och ge den behörighet](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).
* [Skapa en resursgrupp, en princip och en principtilldelning](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar roller finns i [Hantera åtkomst till Azure-resurser med hjälp av RBAC- och Azure Resource Manager-mallar](../../role-based-access-control/role-assignments-template.md).
* Ett exempel på distribution av arbetsyteinställningar för Azure Security Center finns i [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Exempelmallar finns på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Du kan också distribuera mallar på [hanteringsgruppsnivå](deploy-to-management-group.md) och [klientnivå](deploy-to-tenant.md).
