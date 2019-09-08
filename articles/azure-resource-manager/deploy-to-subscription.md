---
title: Skapa resurs grupp och resurser vid prenumerationen – Azure Resource Manager mall
description: Beskriver hur du skapar en resurs grupp i en Azure Resource Manager-mall. Det visar också hur du distribuerar resurser i Azures prenumerations omfång.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: tomfitz
ms.openlocfilehash: 37f2b04a62d94cce42b095540380460c38bc5b79
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772946"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Skapa resurs grupper och resurser på prenumerations nivå

Normalt distribuerar du Azure-resurser till en resurs grupp i din Azure-prenumeration. Men du kan också skapa Azure-resurs grupper och skapa Azure-resurser på prenumerations nivån. Om du vill distribuera mallar på prenumerations nivå använder du Azure CLI och Azure PowerShell. Azure Portal har inte stöd för distribution på prenumerations nivån.

Skapa en resurs grupp i en Azure Resource Manager mall genom att definiera en resurs för [Microsoft. Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) med ett namn och en plats för resurs gruppen. Du kan skapa en resurs grupp och distribuera resurser till den resurs gruppen i samma mall. De resurser som du kan distribuera på prenumerations nivån är: [Principer](../governance/policy/overview.md)och [rollbaserad åtkomst kontroll](../role-based-access-control/overview.md).

## <a name="deployment-considerations"></a>Distributionsöverväganden

Distribution av prenumerations nivå skiljer sig från distribution av resurs grupper i följande aspekter:

### <a name="schema-and-commands"></a>Schema och kommandon

Schemat och kommandona som används för distributioner på prenumerations nivå skiljer sig från resurs grupp distributioner. 

För schemat använder `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`du.

För kommandot Azure CLI-distribution använder du [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Följande CLI-kommando distribuerar exempelvis en mall för att skapa en resurs grupp:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

För kommandot PowerShell-distribution använder du [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). Följande PowerShell-kommando distribuerar exempelvis en mall för att skapa en resurs grupp:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Distributions namn och plats

När du distribuerar till din prenumeration måste du ange en plats för distributionen. Du kan också ange ett namn för distributionen. Om du inte anger ett namn för distributionen används namnet på mallen som distributions namn. Om du till exempel distribuerar en mall med namnet **azuredeploy. JSON** skapas ett standard distributions namn för **azuredeploy**.

Platsen för distributioner på prenumerations nivå är oföränderlig. Du kan inte skapa en distribution på en plats om det finns en befintlig distribution med samma namn men annan plats. Om du får fel koden `InvalidDeploymentLocation`använder du antingen ett annat namn eller samma plats som den tidigare distributionen för det namnet.

### <a name="use-template-functions"></a>Använda mall funktioner

För distributioner på prenumerations nivå finns det några viktiga överväganden när du använder mallarna:

* Funktionen [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) stöds **inte** .
* Funktionen [resourceId ()](resource-group-template-functions-resource.md#resourceid) stöds. Använd den för att hämta resurs-ID för resurser som används på prenumerations nivå distributioner. Hämta till exempel resurs-ID för en princip definition med`resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* Funktionerna [Reference ()](resource-group-template-functions-resource.md#reference) och [List ()](resource-group-template-functions-resource.md#list) stöds.

## <a name="create-resource-groups"></a>Skapa resurs grupper

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

Du hittar mallens schema [här](/azure/templates/microsoft.resources/allversions). Liknande mallar hittar du på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Skapa flera resurs grupper

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

Information om resurs upprepning finns i [distribuera fler än en instans av en resurs eller egenskap i Azure Resource Manager mallar](./resource-group-create-multiple.md)och [Självstudier: Skapa flera resurs instanser med Resource Manager-](./resource-manager-tutorial-create-multiple-instances.md)mallar.

## <a name="create-resource-group-and-deploy-resources"></a>Skapa resurs grupp och distribuera resurser

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
* Mer information om hur du skapar Azure Resource Manager-mallar finns i [Redigera mallar](resource-group-authoring-templates.md). 
* En lista över tillgängliga funktioner i en mall finns i [Template Functions](resource-group-template-functions.md).
