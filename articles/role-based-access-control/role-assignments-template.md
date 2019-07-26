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
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360488"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Hantera åtkomst till Azure-resurser med RBAC och Azure Resource Manager mallar

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är metoden som du använder när du hanterar åtkomst till Azure-resurser. Förutom att använda Azure PowerShell eller Azure CLI kan du hantera åtkomst till Azure-resurser med RBAC-och [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md). Mallar kan vara användbara om du behöver distribuera resurser konsekvent och upprepade gånger. I den här artikeln beskrivs hur du kan hantera åtkomst med RBAC och mallar.

## <a name="assign-role-to-resource-group-or-subscription"></a>Tilldela roll till resurs grupp eller prenumeration

För att skapa åtkomst i RBAC skapar du rolltilldelningar. Följande mall visar:
- Så här tilldelar du en roll till en användare, grupp eller ett program i antingen resurs gruppen eller prenumerations omfånget
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

Följande visar ett exempel på en roll tilldelning för en användare för en resurs grupp när du har distribuerat mallen.

![Roll tilldelning med hjälp av en mall](./media/role-assignments-template/role-assignment-template.png)

Roll tilldelningens omfattning bestäms från distributions nivån. Både resurs grupps-och prenumerations nivå distributions kommandon visas i den här artikeln.

## <a name="assign-role-to-resource"></a>Tilldela roll till resurs

Om du behöver skapa en roll tilldelning på nivån för en resurs, är formatet för roll tilldelningen annorlunda. Du anger resurs leverantörens namn område och resurs typ för den resurs som rollen ska tilldelas till. Du inkluderar också namnet på resursen i namnet på roll tilldelningen.

Använd följande format för roll tilldelningens typ och namn:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Följande mall distribuerar ett lagrings konto och tilldelar den rollen. Du distribuerar den med resurs grupps kommandon.

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

Följande visar ett exempel på en deltagar roll tilldelning till en användare för ett lagrings konto när du har distribuerat mallen.

![Roll tilldelning med hjälp av en mall](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>Distribuera mall med Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Följ dessa steg om du vill distribuera den tidigare mallen till antingen en resurs grupp eller en prenumeration med hjälp av Azure PowerShell.

1. Skapa en ny fil med namnet RBAC-rg. JSON och kopiera den tidigare mallen.

1. Logga in till [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Hämta den unika identifieraren för en användare, grupp eller ett program. Du kan till exempel använda kommandot [Get-AzADUser](/powershell/module/az.resources/get-azaduser) för att visa en lista över Azure AD-användare.

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. Mallen genererar ett standardvärde för det GUID som används för att identifiera roll tilldelningen. Om du behöver ange ett särskilt GUID skickar du det värdet i för parametern roleNameGuid. Identifieraren har formatet:`11111111-1111-1111-1111-111111111111`

Följ dessa steg om du vill tilldela rollen på nivån för en resurs eller resurs grupp:

1. Skapa en resurs grupp för ett exempel.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Använd kommandot [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) för att starta distributionen.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    Nedan visas ett exempel på utdata.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Om du vill tilldela rollen till en prenumerations nivå använder du kommandot [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) och anger en plats för distributionen.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

Den har liknande utdata till distributions kommandot för resurs grupper.

## <a name="deploy-template-using-the-azure-cli"></a>Distribuera mallen med hjälp av Azure CLI

Följ dessa steg om du vill distribuera den tidigare mallen med hjälp av Azure CLI till antingen en resurs grupp eller en prenumeration.

1. Skapa en ny fil med namnet RBAC-rg. JSON och kopiera den tidigare mallen.

1. Logga in på [Azure CLI](/cli/azure/authenticate-azure-cli).

1. Hämta den unika identifieraren för en användare, grupp eller ett program. Du kan till exempel använda kommandot [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) för att visa en Azure AD-användare.

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. Mallen genererar ett standardvärde för det GUID som används för att identifiera roll tilldelningen. Om du behöver ange ett särskilt GUID skickar du det värdet i för parametern roleNameGuid. Identifieraren har formatet:`11111111-1111-1111-1111-111111111111`

Följ dessa steg om du vill tilldela rollen på nivån för en resurs eller resurs grupp:

1. Skapa en resurs grupp för ett exempel.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Använd kommandot [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) för att starta distributionen.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    Nedan visas ett exempel på utdata.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

Om du vill tilldela rollen på samma nivå som en prenumeration använder du kommandot [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) och anger en plats för distributionen.

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

Den har liknande utdata till distributions kommandot för resurs grupper.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)
- [Azure-snabbstartmallar](https://azure.microsoft.com/resources/templates/?term=rbac)
