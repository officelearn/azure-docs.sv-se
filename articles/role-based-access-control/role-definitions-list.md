---
title: Lista rolldefinitioner i Azure RBAC med Azure Portal, Azure PowerShell, Azure CLI eller REST API | Microsoft-dokument
description: Lär dig hur du listar inbyggda och anpassade roller i Azure RBAC med Azure-portalen, Azure PowerShell, Azure CLI eller REST API.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aa888eedc81ceb3188f801e273c70722207bf512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062992"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Lista rolldefinitioner i Azure RBAC

En rolldefinition är en samling behörigheter som kan utföras, till exempel läsa, skriva och ta bort. Det brukar bara kallas en roll. [Azure rollbaserad åtkomstkontroll (RBAC)](overview.md) har över 120 [inbyggda roller](built-in-roles.md) eller så kan du skapa dina egna anpassade roller. I den här artikeln beskrivs hur du listar de inbyggda och anpassade roller som du kan använda för att bevilja åtkomst till Azure-resurser.

Information om hur du visar listan över administratörsroller för Azure Active Directory finns [i Administratörsrollbehörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>Lista alla roller

Följ dessa steg för att lista alla roller i Azure-portalen.

1. Klicka på Alla **tjänster** i Azure-portalen och välj sedan ett scope. Du kan till exempel välja **Hanteringsgrupper,** **Prenumerationer,** **Resursgrupper**eller en resurs.

1. Klicka på den specifika resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Roller** om du vill visa en lista över alla inbyggda och anpassade roller.

   Du kan se antalet användare och grupper som har tilldelats varje roll med det aktuella scopet.

   ![Lista över roller](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Lista alla roller

Om du vill visa en lista över alla roller i Azure PowerShell använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>Lista en rolldefinition

Om du vill ange information om en viss roll använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>Lista en rolldefinition i JSON-format

Om du vill visa en lista över en roll i JSON-format använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Lista behörigheter för en rolldefinition

Om du vill visa behörigheter för en viss roll använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>Lista alla roller

Om du vill visa en lista över alla roller i Azure CLI använder du [listan över az-rolldefinition](/cli/azure/role/definition#az-role-definition-list).

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

I följande exempel visas alla inbyggda roller.

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

### <a name="list-a-role-definition"></a>Lista en rolldefinition

Om du vill visa information om en roll använder du [listan över az-rolldefinition](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition list --name <role_name>
```

I följande exempel visas rolldefinitionen *deltagare:*

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
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
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

### <a name="list-permissions-of-a-role-definition"></a>Lista behörigheter för en rolldefinition

I följande exempel visas bara *åtgärder* och *notActions* för *rollen Deltagare.*

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

I följande exempel visas bara åtgärderna för rollen *deltagare i virtuell dator.*

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

## <a name="rest-api"></a>REST API

### <a name="list-role-definitions"></a>Visa lista över rolldefinitioner

Om du vill visa rolldefinitioner använder du [rolldefinitioner - LISTA](/rest/api/authorization/roledefinitions/list) REST API. Om du vill förfina resultaten anger du ett scope och ett valfritt filter.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. I URI:et ersätter du *{scope}* med det scope som du vill visa rolldefinitionerna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |

    I föregående exempel är microsoft.web en resursleverantör som refererar till en App Service-instans. På samma sätt kan du använda andra resursleverantörer och ange omfånget. Mer information finns i [Azure Resource-providers och -typer](../azure-resource-manager/management/resource-providers-and-types.md) och som stöds av [Azure Resource Manager-resursprovideråtgärder](resource-provider-operations.md).  
     
1. Ersätt *{filter}* med det villkor som du vill använda för att filtrera rolldefinitionslistan.

    > [!div class="mx-tableFixed"]
    > | Filter | Beskrivning |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Visar rolldefinitioner för det angivna scopet och eventuella delscopen. |
    > | `$filter=type+eq+'{type}'` | Visar rolldefinitioner av den angivna typen. Typ av roll `CustomRole` `BuiltInRole`kan vara eller . |

### <a name="list-a-role-definition"></a>Lista en rolldefinition

Om du vill lista information om en viss roll använder du [rolldefinitionerna - Get](/rest/api/authorization/roledefinitions/get) eller [Role Definitions - Get By Id](/rest/api/authorization/roledefinitions/getbyid) REST API.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    För en rolldefinition på katalognivå kan du använda den här begäran:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI:et ersätter du *{scope}* med det scope som du vill visa rolldefinitionen för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |
     
1. Ersätt *{roleDefinitionId}* med rolldefinitionsidentifieraren.

## <a name="next-steps"></a>Nästa steg

- [Inbyggda roller för Azure-resurser](built-in-roles.md)
- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Lista rolltilldelningar med Azure RBAC och Azure-portalen](role-assignments-list-portal.md)
- [Lägga till eller ta bort rolltilldelningar med Azure RBAC och Azure-portalen](role-assignments-portal.md)
