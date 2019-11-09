---
title: Distribuera prenumerations nivå – Azure Resource Manager mall
description: Beskriver hur du skapar en resurs grupp i en Azure Resource Manager-mall. Det visar också hur du distribuerar resurser i Azures prenumerations omfång.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: tomfitz
ms.openlocfilehash: 04a46700b68bcf498956f93c96ce2dccf1b555fe
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832722"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Skapa resurs grupper och resurser på prenumerations nivå

Normalt distribuerar du Azure-resurser till en resurs grupp i din Azure-prenumeration. Men du kan också skapa resurser på prenumerations nivå. Du använder distributioner på prenumerations nivå för att vidta åtgärder som är begripliga på den nivån, till exempel att skapa resurs grupper eller tilldela [rollbaserad åtkomst kontroll](../role-based-access-control/overview.md).

Om du vill distribuera mallar på prenumerations nivån använder du Azure CLI, PowerShell eller REST API. Azure Portal har inte stöd för distribution på prenumerations nivån.

## <a name="supported-resources"></a>Resurser som stöds

Du kan distribuera följande resurs typer på prenumerations nivån:

* [distributioner](/azure/templates/microsoft.resources/deployments)
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Schemat som används för distributioner på prenumerations nivå skiljer sig från schemat för distributioner av resurs grupper.

För mallar använder du:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

För parameter-filer använder du:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Distributions kommandon

Kommandona för distributioner på prenumerations nivå skiljer sig från kommandona för resurs grupp distributioner.

Använd [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)för Azure CLI. I följande exempel distribueras en mall för att skapa en resurs grupp:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


För kommandot PowerShell-distribution använder du [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). I följande exempel distribueras en mall för att skapa en resurs grupp:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

För REST API använder du [distributioner – skapa vid prenumerations omfång](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Distributions plats och namn

För distributioner på prenumerations nivå måste du ange en plats för distributionen. Platsen för distributionen är separat från platsen för de resurser som du distribuerar. Distributions platsen anger var distributions data ska lagras.

Du kan ange ett namn för distributionen eller använda standard distributions namnet. Standard namnet är namnet på mallfilen. Om du till exempel distribuerar en mall med namnet **azuredeploy. JSON** skapas ett standard distributions namn för **azuredeploy**.

För varje distributions namn är platsen oföränderlig. Du kan inte skapa en distribution på en plats om det finns en befintlig distribution med samma namn på en annan plats. Om du får felkoden `InvalidDeploymentLocation`använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

## <a name="use-template-functions"></a>Använda mall funktioner

För distributioner på prenumerations nivå finns det några viktiga överväganden när du använder mallarna:

* Funktionen [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) stöds **inte** .
* Funktionen [resourceId ()](resource-group-template-functions-resource.md#resourceid) stöds. Använd den för att hämta resurs-ID för resurser som används på prenumerations nivå distributioner. Du kan till exempel hämta resurs-ID för en princip definition med `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`. Du kan också använda funktionen [subscriptionResourceId ()](resource-group-template-functions-resource.md#subscriptionresourceid) för att hämta resurs-ID för en resurs på en prenumerations nivå.
* Funktionerna [Reference ()](resource-group-template-functions-resource.md#reference) och [List ()](resource-group-template-functions-resource.md#list) stöds.

## <a name="create-resource-groups"></a>Skapa resurs grupper

Skapa en resurs grupp i en Azure Resource Manager mall genom att definiera en resurs för [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) med ett namn och en plats för resurs gruppen. Du kan skapa en resurs grupp och distribuera resurser till den resurs gruppen i samma mall.

Följande mall skapar en tom resurs grupp.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Använd [Kopiera-elementet](resource-group-create-multiple.md) med resurs grupper för att skapa fler än en resurs grupp. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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

Information om resurs upprepning finns i [distribuera fler än en instans av en resurs eller egenskap i Azure Resource Manager mallar](./resource-group-create-multiple.md)och [Självstudier: skapa flera resurs instanser med Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Resurs grupp och resurser

Använd en kapslad mall för att skapa resurs gruppen och distribuera resurser till den. Den kapslade mallen definierar de resurser som ska distribueras till resurs gruppen. Ange att den kapslade mallen är beroende av resurs gruppen för att kontrol lera att resurs gruppen finns innan du distribuerar resurserna.

I följande exempel skapas en resurs grupp och ett lagrings konto distribueras till resurs gruppen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
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

I följande exempel tilldelas en befintlig princip definition till prenumerationen. Om principen använder parametrar, anger du dem som ett objekt. Om principen inte tar parametrar använder du det tomma standard objektet.

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
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
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

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Använd följande om du vill distribuera den här mallen med PowerShell:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Definiera och tilldela princip

Du kan [definiera](../governance/policy/concepts/definition-structure.md) och tilldela en princip i samma mall.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
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
            "name": "location-lock",
            "apiVersion": "2018-05-01",
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

Använd följande CLI-kommando om du vill skapa en princip definition i din prenumeration och tillämpa den på prenumerationen:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Använd följande om du vill distribuera den här mallen med PowerShell:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du tilldelar roller finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure Resource Manager mallar](../role-based-access-control/role-assignments-template.md).
* Ett exempel på distribution av arbets ytans inställningar för Azure Security Center finns i [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Du hittar exempel på mallar på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Mer information om hur du skapar Azure Resource Manager-mallar finns i [Redigera mallar](resource-group-authoring-templates.md). 
* En lista över tillgängliga funktioner i en mall finns i [Template Functions](resource-group-template-functions.md).
