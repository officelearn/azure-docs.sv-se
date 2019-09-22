---
title: Hantera åtkomst till Azure-resurser med RBAC och Azure Resource Manager mallar | Microsoft Docs
description: Lär dig hur du hanterar åtkomst till Azure-resurser för användare, grupper och program med hjälp av rollbaserad åtkomst kontroll (RBAC) och Azure Resource Manager mallar.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b7f701cd3ce07099d80bca40e506108bcc9a9da9
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178110"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Hantera åtkomst till Azure-resurser med RBAC och Azure Resource Manager mallar

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är metoden som du använder när du hanterar åtkomst till Azure-resurser. Förutom att använda Azure PowerShell eller Azure CLI kan du hantera åtkomst till Azure-resurser med hjälp av [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md). Mallar kan vara användbara om du behöver distribuera resurser konsekvent och upprepade gånger. I den här artikeln beskrivs hur du kan hantera åtkomst med RBAC och mallar.

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>Skapa en roll tilldelning i ett resurs grupps omfång (utan parametrar)

För att skapa åtkomst i RBAC skapar du rolltilldelningar. Följande mall visar ett enkelt sätt att skapa en roll tilldelning. Vissa värden anges i mallen. Följande mall visar:

-  Tilldela rollen [läsare](built-in-roles.md#reader) till en användare, grupp eller ett program i ett resurs grupps omfång

Om du vill använda mallen måste du göra följande:

- Skapa en ny JSON-fil och kopiera mallen
- Ersätt `<your-principal-id>` med den unika identifieraren för en användare, grupp eller ett program som rollen ska tilldelas. Identifieraren har formatet:`11111111-1111-1111-1111-111111111111`

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

Här är exempel på [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment) och [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) -kommandon för hur du startar distributionen i en resurs grupp med namnet ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Följande visar ett exempel på roll tilldelningen för en användare för en resurs grupp när du har distribuerat mallen.

![Roll tilldelning i resurs gruppens omfång](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>Skapa en roll tilldelning i en resurs grupp eller ett prenumerations omfång

Den tidigare mallen är inte mycket flexibel. Följande mall använder parametrar och kan användas i olika omfång. Följande mall visar:

- Så här tilldelar du en roll till en användare, grupp eller ett program i antingen en resurs grupp eller ett prenumerations omfång
- Ange rollen ägare, deltagare och läsare som en parameter

Om du vill använda mallen måste du ange följande indata:

- Den unika identifieraren för en användare, grupp eller ett program som rollen ska tilldelas
- Rollen som ska tilldelas
- En unik identifierare som ska användas för roll tilldelningen eller så kan du använda standard identifieraren

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

Om du vill hämta den unika identifieraren för en användare att tilldela rollen till kan du använda kommandona [Get-AzADUser](/powershell/module/az.resources/get-azaduser) eller [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) .

```azurepowershell
$userid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
```

Roll tilldelningens omfattning bestäms från distributions nivån. Här är exempel på [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment) och [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) -kommandon för hur du startar distributionen i ett resurs grupps omfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

Här är exempel på [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) och [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) -kommandon för hur du startar distributionen i ett prenumerations omfång och anger platsen.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

## <a name="create-a-role-assignment-at-a-resource-scope"></a>Skapa en roll tilldelning i ett resurs omfång

Om du behöver skapa en roll tilldelning på nivån för en resurs, är formatet för roll tilldelningen annorlunda. Du anger resurs leverantörens namn område och resurs typ för den resurs som rollen ska tilldelas till. Du inkluderar också namnet på resursen i namnet på roll tilldelningen.

Använd följande format för roll tilldelningens typ och namn:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Följande mall visar:

- Så här skapar du ett nytt lagringskonto
- Så här tilldelar du en roll till en användare, grupp eller ett program i lagrings konto omfånget
- Ange rollen ägare, deltagare och läsare som en parameter

Om du vill använda mallen måste du ange följande indata:

- Den unika identifieraren för en användare, grupp eller ett program som rollen ska tilldelas
- Rollen som ska tilldelas
- En unik identifierare som ska användas för roll tilldelningen eller så kan du använda standard identifieraren


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
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
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

Om du vill distribuera den tidigare mallen använder du resurs grupps kommandon. Här är exempel på [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment) och [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) -kommandon för hur du startar distributionen i ett resurs omfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Contributor
```

Följande visar ett exempel på roll tilldelningen deltagare till en användare för ett lagrings konto när du har distribuerat mallen.

![Roll tilldelning i resurs omfång](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>Skapa en roll tilldelning för ett nytt huvud namn för tjänsten

Om du skapar ett nytt huvud namn för tjänsten och sedan omedelbart försöker tilldela en roll till tjänstens huvud namn kan roll tilldelningen inte utföras i vissa fall. Om du till exempel skapar en ny hanterad identitet och sedan försöker tilldela en roll till tjänstens huvud namn i samma Azure Resource Manager mall kan roll tilldelningen Miss Miss förväntat. Orsaken till det här felet är förmodligen en fördröjning i replikeringen. Tjänstens huvud namn skapas i en region. roll tilldelningen kan dock inträffa i en annan region som ännu inte har replikerat tjänstens huvud namn. För att åtgärda det här scenariot ska du `principalType` ställa in `ServicePrincipal` egenskapen till när roll tilldelningen skapas.

Följande mall visar:

- Så här skapar du en ny hanterad identitet för tjänstens huvud namn
- Så här anger du`principalType`
- Tilldela deltagar rollen till tjänstens huvud namn i ett resurs grupps omfång

Om du vill använda mallen måste du ange följande indata:

- Det grundläggande namnet på den hanterade identiteten eller så kan du använda standard strängen

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

Här är exempel på [New-AzResourceGroupDeployment-](/powershell/module/az.resources/new-azresourcegroupdeployment) och [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) -kommandon för hur du startar distributionen i ett resurs grupps omfång.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Följande visar ett exempel på roll tilldelningen deltagare till ett nytt hanterat identitets tjänst huvud objekt när du har distribuerat mallen.

![Roll tilldelning för en ny hanterad identitets tjänstens huvud namn](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)
- [Skapa resurs grupper och resurser på prenumerations nivå](../azure-resource-manager/deploy-to-subscription.md)
- [Azure-snabbstartmallar](https://azure.microsoft.com/resources/templates/?term=rbac)
