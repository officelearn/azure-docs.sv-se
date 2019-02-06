---
title: Skapa resursgrupp och resurser på prenumeration – Azure Resource Manager-mall
description: Beskriver hur du skapar en resursgrupp i en Azure Resource Manager-mall. Den visar också hur du distribuerar resurser i omfånget för Azure-prenumeration.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: ce95406cbcb6d9514159d84d653a9ff361713aec
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744406"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Skapa resursgrupper och resurser på prenumerationsnivån

Normalt distribuerar du Azure-resurser till en resursgrupp i Azure-prenumerationen. Men kan du också skapa Azure-resursgrupper och skapa Azure-resurser på prenumerationsnivån. För att distribuera mallar på prenumerationsnivå måste använda du Azure CLI och Azure PowerShell. Azure-portalen stöder inte distribution på prenumerationsnivå.

Om du vill skapa en resursgrupp i en Azure Resource Manager-mall definierar en [ **Microsoft.Resources/resourceGroups** ](/azure/templates/microsoft.resources/allversions) resurs med ett namn och plats för resursgruppen. Du kan skapa en resursgrupp och distribuera resurser till resursgruppen i samma mall. De resurser som du kan distribuera på prenumerationsnivå är: [Principer](../azure-policy/azure-policy-introduction.md), och [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-considerations"></a>Distributionsöverväganden

Nivån prenumerationsdistributionen skiljer sig från distribution av resursgrupper i följande aspekter:

### <a name="schema-and-commands"></a>Schema- och kommandon

Schema- och kommandon som du använder för prenumerationsnivå distributioner skiljer sig från distribution av resursgrupper. 

Schemat, använda `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Azure CLI-kommando för distribution använder [az distributionen skapa](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Till exempel distribuerar följande CLI-kommando en mall för att skapa en resursgrupp:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

PowerShell-kommando för distribution använder [New AzDeployment](/powershell/module/az.resources/new-azdeployment). Till exempel distribuerar följande PowerShell-kommando en mall för att skapa en resursgrupp:

```azurepowershell
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Namn och plats

När du distribuerar till din prenumeration, måste du ange en plats för distributionen. Du kan också ange ett namn för distributionen. Om du inte anger ett namn för distributionen, används namnet på mallen som distributionens namn. Till exempel distribuerar en mall med namnet **azuredeploy.json** skapar ett standardnamn för distribution av **azuredeploy**.

Platsen för prenumerationen på distributioner kan inte ändras. Du kan inte skapa en distribution i en plats när det finns en befintlig distribution med samma namn men olika plats. Om du får felkoden `InvalidDeploymentLocation`, antingen använda ett annat namn eller på samma plats som den föregående distributionen för det namnet.

### <a name="use-template-functions"></a>Använda Mallfunktioner

För prenumerationsnivå distributioner finns det några viktiga överväganden när du använder Mallfunktioner:

* Den [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) funktionen är **inte** stöds.
* Den [resourceId()](resource-group-template-functions-resource.md#resourceid) funktionen stöds. Du kan använda den för att hämta resurs-ID för resurser som används vid prenumeration på distributioner. Till exempel hämta resurs-ID för en principdefinition med `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* Den [reference()](resource-group-template-functions-resource.md#reference) och [list()](resource-group-template-functions-resource.md#list) funktioner stöds.

## <a name="create-resource-groups"></a>Skapa resursgrupper

Följande mall skapar en tom resursgrupp.

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

Mallsschemat finns på [här](/azure/templates/microsoft.resources/allversions). Liknande mallar finns på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Skapa flera resursgrupper

Använd den [kopieringselement](resource-group-create-multiple.md) med resursgrupper för att skapa fler än en resursgrupp. 

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

Läs om hur resource iteration [distribuera fler än en instans av en resurs eller egenskapen i Azure Resource Manager-mallar](./resource-group-create-multiple.md), och [självstudien: Skapa flera resursinstanser med Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="create-resource-group-and-deploy-resources"></a>Skapa resursgrupp och distribuera resurser

Använd en kapslad mall för att skapa en resursgrupp och distribuera resurser till den. Den kapslade mallen definierar resurserna som ska distribueras till resursgruppen. Ange den kapslade mallen som beroende på resursgruppen som ska kontrollera att resursgruppen finns innan du distribuerar resurser.

I följande exempel skapas en resursgrupp och distribuerar ett lagringskonto till resursgruppen.

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

I följande exempel tilldelar en befintlig principdefinitionen till prenumerationen. Om principen tar parametrar kan du ange dem som ett-objekt. Om principen inte tar parametrar, använder du standard tomt-objekt.

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

Om du vill använda en inbyggd princip på Azure-prenumerationen, använder du följande Azure CLI-kommandon:

```azurecli
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Om du vill använda en inbyggd princip på Azure-prenumerationen, använder du följande Azure CLI-kommandon:

```azurecli
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell
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

Du kan [definiera](../azure-policy/policy-definition.md) och tilldela en princip i samma mall.

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

För att skapa principdefinitionen i din prenumeration och tillämpa den på prenumerationen, använder du följande CLI-kommando:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="create-roles"></a>Skapa roller

### <a name="assign-role-at-subscription"></a>Tilldela rollen på prenumerationen

I följande exempel tilldelar en roll till en användare eller grupp för prenumerationen. I det här exemplet kan anger du inte en omfattning för tilldelningen eftersom omfånget ställs automatiskt in prenumerationen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "[guid(parameters('principalId'), deployment().name)]",
            "apiVersion": "2017-09-01",
            "properties": {
                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Om du vill tilldela en Active Directory-grupp till en roll för din prenumeration, använder du följande Azure CLI-kommandon:

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

### <a name="assign-role-at-scope"></a>Tilldela roll i omfånget

Följande prenumerationsnivå mall tilldelar en roll till en användare eller grupp som är begränsad till en resursgrupp i prenumerationen. Omfattningen måste vara på eller under nivån för distribution. Du kan distribuera till en prenumeration och ange en rolltilldelning för en resursgrupp i den prenumerationen. Du kan dock distribuera till en resursgrupp och ange en rolltilldelningsomfattning till prenumerationen.

Om du vill tilldela rollen på ett scope, använda en kapslad distribution. Observera att resursgruppens namn har angetts både i egenskaperna för distribution av resursen och i egenskapen scope rolltilldelningen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        },
        "rgName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "assignRole",
            "resourceGroup": "[parameters('rgName')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/roleAssignments",
                            "name": "[guid(parameters('principalId'), deployment().name)]",
                            "apiVersion": "2017-09-01",
                            "properties": {
                                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                                "principalId": "[parameters('principalId')]",
                                "scope": "[concat(subscription().id, '/resourceGroups/', parameters('rgName'))]"
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

Om du vill tilldela en Active Directory-grupp till en roll för din prenumeration, använder du följande Azure CLI-kommandon:

```azurecli
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json \
  --parameters principalId=$principalid roleDefinitionId=$role rgName demoRg
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id `
  -rgName demoRg
```

## <a name="next-steps"></a>Nästa steg

* Ett exempel för att distribuera inställningar för arbetsyta för Azure Security Center finns i [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Läs om hur du skapar Azure Resource Manager-mallar i [Webbsidemallar](resource-group-authoring-templates.md). 
* En lista över tillgängliga funktioner i en mall finns i [Mallfunktioner](resource-group-template-functions.md).
