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
ms.date: 01/15/2018
ms.author: tomfitz
ms.openlocfilehash: 3fcfd0f3e4f9ca6e94a7213bc60e54560938799b
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077573"
---
# <a name="create-resource-groups-and-resources-for-an-azure-subscription"></a>Skapa resursgrupper och resurser för en Azure-prenumeration

Normalt kan distribuera du resurser till en resursgrupp i Azure-prenumerationen. Du kan dock använda prenumerationsnivå distributioner för att skapa resursgrupper och resurser som gäller för din prenumeration.

Om du vill skapa en resursgrupp i en Azure Resource Manager-mall definierar en **Microsoft.Resources/resourceGroups** resurs med ett namn och plats för resursgruppen. Du kan skapa en resursgrupp och distribuera resurser till resursgruppen i samma mall.

[Principer](../azure-policy/azure-policy-introduction.md), [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md), och [Azure Security Center](../security-center/security-center-intro.md) är tjänster som du vill tillämpa på prenumerationsnivå i stället för resursgruppsnivå.

Den här artikeln visar hur du skapar resursgrupper och hur du skapar resurser som gäller i hela prenumerationen. Det använder Azure CLI och PowerShell för att distribuera mallarna. Du kan inte använda portalen för att distribuera mallarna eftersom portalgränssnittet distribuerar till resursgrupp, inte Azure-prenumeration.

## <a name="schema-and-commands"></a>Schema- och kommandon

Schema- och kommandon som du använder för prenumerationsnivå distributioner skiljer sig från distribution av resursgrupper. 

Schemat, använda `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Azure CLI-kommando för distribution använder [az distributionen skapa](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create).

PowerShell-kommando för distribution använder [New AzDeployment](/powershell/module/az.resources/new-azdeployment).

## <a name="name-and-location"></a>Namn och plats

När du distribuerar till din prenumeration, måste du ange en plats för distributionen. Du kan också ange ett namn för distributionen. Om du inte anger ett namn för distributionen, används namnet på mallen som distributionens namn. Till exempel distribuerar en mall med namnet **azuredeploy.json** skapar ett standardnamn för distribution av **azuredeploy**.

Platsen för prenumerationen på distributioner kan inte ändras. Du kan inte skapa en distribution i en plats när det finns en befintlig distribution med samma namn men olika plats. Om du får felkoden `InvalidDeploymentLocation`, antingen använda ett annat namn eller på samma plats som den föregående distributionen för det namnet.

## <a name="using-template-functions"></a>Med hjälp av Mallfunktioner

För nivån prenumerationsdistributioner finns några viktiga överväganden när du använder Mallfunktioner:

* Den [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) funktionen är **inte** stöds.
* Den [resourceId()](resource-group-template-functions-resource.md#resourceid) funktionen stöds. Du kan använda den för att hämta resurs-ID för resurser som används vid prenumeration på distributioner. Till exempel hämta resurs-ID för en principdefinition med `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* Den [reference()](resource-group-template-functions-resource.md#reference) och [list()](resource-group-template-functions-resource.md#list) funktioner stöds.

## <a name="create-resource-group"></a>Skapa resursgrupp

I följande exempel skapas en tom resursgrupp.

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

Om du vill distribuera den här mallen med Azure CLI, använder du:

```azurecli-interactive
az deployment create \
  -n demoEmptyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoRG rgLocation=northcentralus
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoEmptyRG `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demogroup `
  -rgLocation northcentralus
```

## <a name="create-several-resource-groups"></a>Skapa flera resursgrupper

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

Om du vill distribuera den här mallen med Azure CLI och skapa tre resursgrupper, använder du:

```azurecli-interactive
az deployment create \
  -n demoCopyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json \
  --parameters rgNamePrefix=demoRG rgLocation=northcentralus instanceCount=3
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoCopyRG `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json `
  -rgNamePrefix demogroup `
  -rgLocation northcentralus `
  -instanceCount 3
```

## <a name="create-resource-group-and-deploy-resource"></a>Skapa resursgrupp och distribuera resurser

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

Om du vill distribuera den här mallen med Azure CLI, använder du:

```azurecli-interactive
az deployment create \
  -n demoRGStorage \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json \
  --parameters rgName=rgStorage rgLocation=northcentralus storagePrefix=storage
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoRGStorage `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json `
  -rgName rgStorage `
  -rgLocation northcentralus `
  -storagePrefix storage
```

## <a name="assign-policy"></a>Tilldela princip

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

```azurecli-interactive
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Om du vill använda en inbyggd princip på Azure-prenumerationen, använder du följande Azure CLI-kommandon:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

## <a name="define-and-assign-policy"></a>Definiera och tilldela princip

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

```azurecli-interactive
az deployment create \
  -n definePolicy \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell-interactive
New-AzDeployment `
  -Name definePolicy `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="assign-role-at-subscription"></a>Tilldela rollen på prenumerationen

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

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell-interactive
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

## <a name="assign-role-at-scope"></a>Tilldela roll i omfånget

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

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json \
  --parameters principalId=$principalid roleDefinitionId=$role rgName demoRg
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell-interactive
$role = Get-AzRoleDefinition -Name Contributor

$adgroup = Get-AzADGroup -DisplayName demogroup

New-AzDeployment `
  -Name demoRole `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scopedRoleAssign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id `
  -rgName demoRg
```

## <a name="next-steps"></a>Nästa steg
* Ett exempel för att distribuera inställningar för arbetsyta för Azure Security Center finns i [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Läs om hur du skapar Azure Resource Manager-mallar i [Webbsidemallar](resource-group-authoring-templates.md). 
* En lista över tillgängliga funktioner i en mall finns i [Mallfunktioner](resource-group-template-functions.md).
