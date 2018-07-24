---
title: Hantera åtkomst med RBAC och Azure Resource Manager-mallar | Microsoft Docs
description: Lär dig mer om att hantera åtkomst för användare, grupper och program med hjälp av rollbaserad åtkomstkontroll (RBAC) och Azure Resource Manager-mallar.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5e080614d4f0001a0bf1b44dd402f37db2463e03
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206132"
---
# <a name="manage-access-using-rbac-and-azure-resource-manager-templates"></a>Hantera åtkomst med RBAC och Azure Resource Manager-mallar

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är sättet som du hantera åtkomst till resurser i Azure. Förutom att använda Azure PowerShell eller Azure CLI kan du hantera åtkomst till Azure-resurser med RBAC och [Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md). Mallar kan vara användbart om du vill distribuera resurser konsekvent och upprepade gånger. Den här artikeln beskrivs hur du kan hantera åtkomst med RBAC och mallar.

## <a name="example-template-to-create-a-role-assignment"></a>Exempelmall för att skapa en rolltilldelning

För att skapa åtkomst i RBAC skapar du rolltilldelningar. Följande mall visar:
- Tilldela en roll till en användare, grupp eller program på resursgruppomfånget
- Så här anger du rollerna som ägare, deltagare och läsare som en parameter

Om du vill använda mallen måste du ange följande indata:
- Namnet på en resursgrupp
- Den unika identifieraren för en användare, grupp eller program för att tilldela rollen
- Rollen för att tilldela
- En unik identifierare som används för rolltilldelningen

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
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "scope": "[resourceGroup().id]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2017-05-01",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('scope')]"
      }
    }
  ]
}
```

Nedan visas ett exempel på en läsare rolltilldelning till en användare när du har distribuerat mallen.

![Rolltilldelning med hjälp av en mall](./media/role-assignments-template/role-assignment-template.png)

## <a name="deploy-template-using-azure-powershell"></a>Distribuera mallen med hjälp av Azure PowerShell

Följ dessa steg om du vill distribuera föregående mall med Azure PowerShell.

1. Skapa en ny fil med namnet rbac-rg.json och kopiera den tidigare mallen.

1. Logga in till [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Hämta den unika identifieraren för en användare, grupp eller program. Du kan till exempel använda den [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) kommando för att lista Azure AD-användare.

    ```azurepowershell
    Get-AzureRmADUser
    ```

1. Använd ett GUID-verktyg för att generera en unik identifierare som används för rolltilldelningen. Identifieraren har formatet: `11111111-1111-1111-1111-111111111111`

1. Skapa en exempel-resursgrupp.

    ```azurepowershell
    New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Använd den [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) kommando för att starta distributionen.

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    ```

    Du uppmanas att ange de obligatoriska parametrarna. Nedan visas ett exempel på utdata.

    ```Output
    PS /home/user> New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    
    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    principalId: 22222222-2222-2222-2222-222222222222
    builtInRoleType: Reader
    roleNameGuid: 11111111-1111-1111-1111-111111111111
    
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

## <a name="deploy-template-using-the-azure-cli"></a>Distribuera mallen med hjälp av Azure CLI

Följ dessa steg om du vill distribuera föregående mall med Azure CLI.

1. Skapa en ny fil med namnet rbac-rg.json och kopiera den tidigare mallen.

1. Logga in på den [Azure CLI](/cli/azure/authenticate-azure-cli).

1. Hämta den unika identifieraren för en användare, grupp eller program. Du kan till exempel använda den [az ad user list](/cli/azure/ad/user#az-ad-user-list) kommando för att lista Azure AD-användare.

    ```azurecli
    az ad user list
    ```

1. Använd ett GUID-verktyg för att generera en unik identifierare som används för rolltilldelningen. Identifieraren har formatet: `11111111-1111-1111-1111-111111111111`

1. Skapa en exempel-resursgrupp.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Använd den [az group deployment skapa](/cli/azure/group/deployment#az-group-deployment-create) kommando för att starta distributionen.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    ```

    Du uppmanas att ange de obligatoriska parametrarna. Nedan visas ett exempel på utdata.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    Please provide string value for 'principalId' (? for help): 22222222-2222-2222-2222-222222222222
    Please provide string value for 'builtInRoleType' (? for help):
     [1] Owner
     [2] Contributor
     [3] Reader
    Please enter a choice [1]: 3
    Please provide string value for 'roleNameGuid' (? for help): 11111111-1111-1111-1111-111111111111
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
    
## <a name="next-steps"></a>Nästa steg

- [Skapa och distribuera din första Azure Resource Manager-mall](../azure-resource-manager/resource-manager-create-first-template.md)
- [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)
- [Azure-Snabbstartmallar](https://azure.microsoft.com/resources/templates/?term=rbac)
