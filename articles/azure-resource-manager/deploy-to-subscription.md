---
title: Distribuera resurser till Azure-prenumeration | Microsoft Docs
description: Beskriver hur du skapar en Azure Resource Manager-mall som distribuerar resurser prenumerationsområde.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2018
ms.author: tomfitz
ms.openlocfilehash: 9a9fe16f562805f1bfd6f51af063531f34ffdde2
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308506"
---
# <a name="deploy-resources-to-an-azure-subscription"></a>Distribuera resurser till en Azure-prenumeration

Normalt kan distribuera du resurser till en resursgrupp i Azure-prenumerationen. Vissa resurser kan dock distribueras på nivån för din Azure-prenumeration. De här resurserna använda i din prenumeration. [Principer](../azure-policy/azure-policy-introduction.md), [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md), och [Azure Security Center](../security-center/security-center-intro.md) är tjänster som du vill tillämpa på prenumerationsnivå i stället för resursgruppsnivå.

Den här artikeln använder Azure CLI och PowerShell för att distribuera mallarna. Du kan inte använda portalen för att distribuera mallarna eftersom portalgränssnittet distribuerar till resursgrupp, inte Azure-prenumeration.

## <a name="name-and-location-for-deployment"></a>Namn och plats för distribution

När du distribuerar till din prenumeration, måste du ange en plats för distributionen. Du kan också ange ett namn för distributionen. Om du inte anger ett namn för distributionen, används namnet på mallen som distributionens namn. Till exempel distribuerar en mall med namnet **azuredeploy.json** skapar ett standardnamn för distribution av **azuredeploy**.

Platsen för prenumerationen på distributioner kan inte ändras. Du kan inte skapa en distribution i en plats när det finns en befintlig distribution med samma namn men olika plats. Om du får felkoden `InvalidDeploymentLocation`, antingen använda ett annat namn eller på samma plats som den föregående distributionen för det namnet.

## <a name="using-template-functions"></a>Med hjälp av Mallfunktioner

För nivån prenumerationsdistributioner finns några viktiga överväganden när du använder Mallfunktioner:

* Den [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) funktionen är **inte** stöds.
* Den [resourceId()](resource-group-template-functions-resource.md#resourceid) funktionen stöds. Du kan använda den för att hämta resurs-ID för resurser som används vid prenumeration på distributioner. Till exempel hämta resurs-ID för en principdefinition med `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* Den [reference()](resource-group-template-functions-resource.md#reference) och [list()](resource-group-template-functions-resource.md#list) funktioner stöds.

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

Om du vill använda en inbyggd princip på Azure-prenumerationen, använder du följande Azure CLI-kommandon. I det här exemplet saknar principen parametrar

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
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Om du vill använda en inbyggd princip på Azure-prenumerationen, använder du följande Azure CLI-kommandon. I det här exemplet har principen parametrar.

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
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzureRmDeployment `
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

För att skapa principdefinitionen i din prenumeration och tillämpa den på prenumerationen, använder du följande CLI-kommando.

```azurecli-interactive
az deployment create \
  -n definePolicy \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Om du vill distribuera den här mallen med PowerShell använder du:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name definePolicy `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="assign-role"></a>Tilldela roll

I följande exempel tilldelar en roll till en användare eller grupp.

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

Om du vill tilldela en Active Directory-grupp till en roll för din prenumeration, använder du följande Azure CLI-kommandon.

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
$role = Get-AzureRmRoleDefinition -Name Contributor

$adgroup = Get-AzureRmADGroup -DisplayName demogroup

New-AzureRmDeployment `
  -Name demoRole `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

## <a name="next-steps"></a>Nästa steg
* Ett exempel för att distribuera inställningar för arbetsyta för Azure Security Center finns i [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Om du vill skapa en resursgrupp, [skapa resursgrupper i Azure Resource Manager-mallar](create-resource-group-in-template.md).
* Läs om hur du skapar Azure Resource Manager-mallar i [Webbsidemallar](resource-group-authoring-templates.md). 
* En lista över tillgängliga funktioner i en mall finns i [Mallfunktioner](resource-group-template-functions.md).

