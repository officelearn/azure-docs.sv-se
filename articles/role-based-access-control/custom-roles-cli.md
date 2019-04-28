---
title: Skapa anpassade roller för Azure-resurser med Azure CLI | Microsoft Docs
description: Lär dig hur du skapar anpassade roller med rollbaserad åtkomstkontroll (RBAC) för Azure-resurser med Azure CLI. Detta omfattar att lista, skapa, uppdatera och ta bort anpassade roller.
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
ms.openlocfilehash: ebced83346a7b130598e4a5f49a72d51ffd18e4f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118781"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Skapa anpassade roller för Azure-resurser med Azure CLI

Om de [inbyggda rollerna för Azure-resurser](built-in-roles.md) inte uppfyller organisationens specifika krav, kan du skapa egna anpassade roller. Den här artikeln beskriver hur du skapar och hanterar anpassade roller med hjälp av Azure CLI.

En stegvis självstudiekurs om hur du skapar en anpassad roll finns i [självstudien: Skapa en anpassad roll för Azure-resurser med Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill skapa anpassade roller, behöver du:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)
- [Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa anpassade roller som är tillgängliga för tilldelning, använda [az role definition list](/cli/azure/role/definition#az-role-definition-list). I följande exempel listas alla anpassade roller i den aktuella prenumerationen.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Visa en anpassad rolldefinition

Om du vill visa en anpassad rolldefinition använda [az role definition list](/cli/azure/role/definition#az-role-definition-list). Det här är samma kommando som du använder för en inbyggd roll.

```azurecli
az role definition list --name <role_name>
```

I följande exempel visas den *VM-operatorn* rolldefinitionen:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

I följande exempel visar bara åtgärder av den *VM-operatorn* roll:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Du kan skapa en anpassad roll med [skapa az rolldefinitionen](/cli/azure/role/definition#az-role-definition-create). Rolldefinitionen kan vara en JSON-beskrivning eller en sökväg till en fil som innehåller en JSON-beskrivning.

```azurecli
az role definition create --role-definition <role_definition>
```

I följande exempel skapas en anpassad roll med namnet *VM-operatorn*. Den här anpassade rollen tilldelar åtkomst till alla läsåtgärder av *Microsoft.Compute*, *Microsoft.Storage*, och *Microsoft.Network* leverantörer och tilldelar åtkomst till Om du vill starta, starta om och övervaka virtuella datorer. Den här anpassade rollen kan användas i två prenumerationer. Det här exemplet används en JSON-fil som indata.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

Om du vill uppdatera en anpassad roll först använda [az role definition list](/cli/azure/role/definition#az-role-definition-list) att hämta rolldefinitionen. Dessutom gör ändringarna i rolldefinitionen. Använd slutligen [az role definition update](/cli/azure/role/definition#az-role-definition-update) att spara den uppdaterade rolldefinitionen.

```azurecli
az role definition update --role-definition <role_definition>
```

I följande exempel läggs den *Microsoft.Insights/diagnosticSettings/* åtgärd för att den *åtgärder* av den *VM-operatorn* anpassad roll.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

Ta bort en anpassad roll genom att använda [az rolldefinition ta bort](/cli/azure/role/definition#az-role-definition-delete). Om du vill att rollen ska ta bort, använder du namnet på rollen eller roll-ID. Använd för att fastställa roll-ID, [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

I följande exempel tar bort den *VM-operatorn* anpassad roll.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Skapa en anpassad roll för Azure-resurser med Azure CLI](tutorial-custom-role-cli.md)
- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Azure Resource Manager åtgärder för resursprovider](resource-provider-operations.md)