---
title: Hantera åtkomst till Azure-resurser med RBAC och Azure CLI | Microsoft Docs
description: Lär dig mer om att hantera åtkomst till Azure-resurser för användare, grupper och program med hjälp av rollbaserad åtkomstkontroll (RBAC) och Azure CLI. Detta innefattar hur du listar åtkomst, ger åtkomst och tar bort åtkomst.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8e75a6344e517fb0343343f557cb7211f49cfed8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838331"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Hantera åtkomst till Azure-resurser med RBAC och Azure CLI

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är metoden som du använder när du hanterar åtkomst till Azure-resurser. Den här artikeln beskriver hur du hanterar åtkomst för användare, grupper och program med RBAC och Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

För att hantera åtkomst till något av följande behöver du:

* [Bash i Azure Cloudshell](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>Visa roller

Om du vill visa alla tillgängliga rolldefinitioner använda [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

I följande exempel visas namn och beskrivning av alla tillgängliga rolldefinitioner:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

I följande exempel visar alla inbyggda rolldefinitionerna:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>Lista över en rolldefinition

Om du vill visa en rolldefinition använda [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

I följande exempel visas den *deltagare* rolldefinitionen:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>Lista åtgärder för en roll

I följande exempel visas bara den *åtgärder* och *notActions* av den *deltagare* roll:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

I följande exempel visar bara åtgärder av den *virtuell Datordeltagare* roll:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>Visar åtkomst

I RBAC lista för att lista åtkomstförsök kommer du rolltilldelningar.

### <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

Om du vill visa rolltilldelningar för en viss användare, Använd [az-rolltilldelningslista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Som standard visas endast de tilldelningar som är begränsade till prenumerationen. Du kan visa tilldelningar som omfattar resurs eller grupp `--all`.

I följande exempel visar rolltilldelningar som tilldelas direkt till den *patlong\@contoso.com* användare:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Visa rolltilldelningar för en resursgrupp

Om du vill visa rolltilldelningar som finns för en resursgrupp, använda [az-rolltilldelningslista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

I följande exempel visas rolltilldelningar för den *pharma-sales-projectforecast* resursgrupp:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="grant-access"></a>Bevilja åtkomst

För att skapa åtkomst i RBAC skapar du rolltilldelningar.

### <a name="create-a-role-assignment-for-a-user"></a>Skapa en rolltilldelning för en användare

Använd för att skapa en rolltilldelning för en användare i resursgruppomfånget [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

I följande exempel tilldelas den *virtuell Datordeltagare* rollen *patlong\@contoso.com* användare vid den *pharma-sales-projectforecast* resurs-Gruppomfång:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>Skapa en rolltilldelning för en grupp

Du kan skapa en rolltilldelning för en grupp med [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

I följande exempel tilldelas den *läsare* rollen till den *Ann Mack Team* med ID 22222222-2222-2222-2222-222222222222 prenumerationsområde. Du kan använda för att hämta ID för gruppen [az ad group list](/cli/azure/ad/group#az-ad-group-list) eller [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

I följande exempel tilldelas den *virtuell Datordeltagare* rollen till den *Ann Mack Team* med ID 22222222-2222-2222-2222-222222222222 definitionsområdet resurs för ett virtuellt nätverk med namnet *pharma-sales-projekt – network*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Skapa en rolltilldelning för ett program

Du kan skapa en roll för ett program med [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

I följande exempel tilldelas den *virtuell Datordeltagare* rollen till ett program med objekt-ID 44444444-4444-4444-4444-444444444444 på den *pharma-sales-projectforecast* resursgrupp omfattningen. Du kan använda för att hämta objekt-ID för programmet, [az ad app-lista](/cli/azure/ad/app#az-ad-app-list) eller [az ad app show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Tar bort åtkomst

I RBAC, för att ta bort åtkomst måste du ta bort en rolltilldelning med hjälp av [az-rolltilldelning ta bort](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

I följande exempel tar bort den *virtuell Datordeltagare* rolltilldelning från den *patlong\@contoso.com* användare på den *pharma försäljning projectforecast* resursgrupp:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

I följande exempel tar bort den *läsare* rollen från den *Ann Mack Team* med ID 22222222-2222-2222-2222-222222222222 prenumerationsområde. Du kan använda för att hämta ID för gruppen [az ad group list](/cli/azure/ad/group#az-ad-group-list) eller [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Nästa steg

- [Självstudier: Skapa en anpassad roll för Azure-resurser med Azure CLI](tutorial-custom-role-cli.md)
- [Använda Azure CLI för att hantera Azure-resurser och resursgrupper](../azure-resource-manager/cli-azure-resource-manager.md)