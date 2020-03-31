---
title: Lägga till rolltilldelningar med RBAC- och Azure Resource Manager-mallar
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänsthuvudnamn eller hanterade identiteter med hjälp av Azure Role-based Access Control (RBAC) och Azure Resource Manager-mallar.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f817880f938f5d03024e3aacd9b84817a5ac721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138290"
---
# <a name="add-role-assignments-using-azure-rbac-and-azure-resource-manager-templates"></a>Lägga till rolltilldelningar med Azure RBAC- och Azure Resource Manager-mallar

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Förutom att använda Azure PowerShell eller Azure CLI kan du tilldela roller med [Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md). Mallar kan vara till hjälp om du behöver distribuera resurser konsekvent och upprepade gånger. I den här artikeln beskrivs hur du tilldelar roller med hjälp av mallar.

## <a name="get-object-ids"></a>Hämta objekt-ID:er

Om du vill tilldela en roll måste du ange ID för den användare, grupp eller det program som du vill tilldela rollen till. ID:et har `11111111-1111-1111-1111-111111111111`formatet: . Du kan hämta ID:t med Azure-portalen, Azure PowerShell eller Azure CLI.

### <a name="user"></a>Användare

Om du vill ha en användares ID kan du använda kommandona [Get-AzADUser](/powershell/module/az.resources/get-azaduser) eller [az ad user show.](/cli/azure/ad/user#az-ad-user-show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Grupp

Om du vill ha ID för en grupp kan du använda kommandona [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) eller [az ad group show.](/cli/azure/ad/group#az-ad-group-show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Program

Om du vill hämta ID:t för ett tjänsthuvudnamn (identitet som används av ett program) kan du använda kommandona [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) eller [az ad sp.](/cli/azure/ad/sp#az-ad-sp-list) För ett tjänsthuvudnamn använder du objekt-ID:t och **inte** program-ID: et.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

I RBAC, för att bevilja åtkomst, lägger du till en rolltilldelning.

### <a name="resource-group-without-parameters"></a>Resursgrupp (utan parametrar)

Följande mall visar ett grundläggande sätt att lägga till en rolltilldelning. Vissa värden anges i mallen. Följande mall visar:

-  Så här tilldelar du [rollen Läsare](built-in-roles.md#reader) till en användare, grupp eller ett program i ett resursgruppomfång

Om du vill använda mallen måste du göra följande:

- Skapa en ny JSON-fil och kopiera mallen
- Ersätt `<your-principal-id>` med ID:t för en användare, grupp eller ett program för att tilldela rollen till

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Här är exempel på kommandon för [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [az-gruppdistribution skapar](/cli/azure/group/deployment#az-group-deployment-create) kommandon för hur du startar distributionen i en resursgrupp med namnet ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Följande visar ett exempel på läsarrolltilldelningen till en användare för en resursgrupp efter att mallen har distribuerats.

![Rolltilldelning vid resursgruppomfång](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>Resursgrupp eller prenumeration

Den tidigare mallen är inte särskilt flexibel. Följande mall använder parametrar och kan användas i olika scope. Följande mall visar:

- Så här tilldelar du en roll till en användare, grupp eller ett program i antingen en resursgrupp eller ett prenumerationsomfång
- Så här anger du rollerna Ägare, Deltagare och Läsare som en parameter

Om du vill använda mallen måste du ange följande indata:

- ID:t för en användare, grupp eller ett program som ska tilldela rollen till
- Ett unikt ID som ska användas för rolltilldelningen, eller så kan du använda standard-ID

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Den här mallen är inte idempotent om inte samma `roleNameGuid` värde anges som en parameter för varje distribution av mallen. Om `roleNameGuid` inget anges genereras som standard ett nytt GUID för varje distribution `Conflict: RoleAssignmentExists` och efterföljande distributioner misslyckas med ett fel.

Omfattningen av rolltilldelningen bestäms från distributionsnivån. Här är exempel på kommandon för [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [az-gruppdistribution skapar](/cli/azure/group/deployment#az-group-deployment-create) kommandon för hur du startar distributionen på ett resursgruppomfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Här är exempel på kommandon för [ny-AzDeployment](/powershell/module/az.resources/new-azdeployment) och [az-distribution skapar](/cli/azure/deployment#az-deployment-create) kommandon för hur du startar distributionen på ett prenumerationsomfång och anger platsen.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>Resurs

Om du behöver lägga till en rolltilldelning på resursnivå är rolltilldelningens format olika. Du anger resursleverantörens namnområde och resurstyp för den resurs som du vill tilldela rollen till. Du kan också inkludera namnet på resursen i namn på rolltilldelningen.

Använd följande format för typ och namn på rolltilldelningen:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Följande mall visar:

- Så här skapar du ett nytt lagringskonto
- Så här tilldelar du en roll till en användare, grupp eller ett program i scopet för lagringskontot
- Så här anger du rollerna Ägare, Deltagare och Läsare som en parameter

Om du vill använda mallen måste du ange följande indata:

- ID:t för en användare, grupp eller ett program som ska tilldela rollen till

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Om du vill distribuera den tidigare mallen använder du resursgruppskommandona. Här är exempel på kommandon för [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [az-gruppdistribution skapar](/cli/azure/group/deployment#az-group-deployment-create) kommandon för hur du startar distributionen i ett resursomfattning.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Följande visar ett exempel på deltagarrolltilldelningen till en användare för ett lagringskonto efter distribution av mallen.

![Rolltilldelning vid resursomfattning](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nytt huvudman för tjänsten

Om du skapar ett nytt tjänsthuvudnamn och omedelbart försöker tilldela en roll till tjänstens huvudnamn, kan den rolltilldelningen misslyckas i vissa fall. Om du till exempel skapar en ny hanterad identitet och sedan försöker tilldela en roll till tjänstens huvudnamn i samma Azure Resource Manager-mall, kan rolltilldelningen misslyckas. Orsaken till detta fel är sannolikt en replikeringsfördröjning. Tjänstens huvudnamn skapas i en region. Rolltilldelningen kan dock förekomma i en annan region som inte har replikerat tjänstens huvudnamn ännu. Om du vill åtgärda det `principalType` här `ServicePrincipal` scenariot bör du ange egenskapen till när du skapar rolltilldelningen.

Följande mall visar:

- Så här skapar du ett nytt huvudnamn för hanterad identitetstjänst
- Så här anger du`principalType`
- Så här tilldelar du rollen Deltagare till tjänsthuvudhuvudet i ett resursgruppomfång

Om du vill använda mallen måste du ange följande indata:

- Basnamnet på den hanterade identiteten, eller så kan du använda standardsträngen

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Här är exempel på kommandon för [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [az-gruppdistribution skapar](/cli/azure/group/deployment#az-group-deployment-create) kommandon för hur du startar distributionen på ett resursgruppomfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Följande visar ett exempel på deltagarrolltilldelningen till ett nytt huvudnamn för hanterad identitetstjänst när mallen har distribuerats.

![Rolltilldelning för ett nytt huvudnamn för hanterad identitetstjänst](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md)
- [Skapa resursgrupper och resurser på prenumerationsnivå](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure-snabbstartmallar](https://azure.microsoft.com/resources/templates/?term=rbac)
