---
title: Skapa eller uppdatera anpassade roller för Azure-resurser med Azure CLI | Microsoft-dokument
description: Lär dig hur du listar, skapar, uppdaterar eller tar bort anpassade roller med rollbaserad åtkomstkontroll (RBAC) för Azure-resurser med Azure CLI.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44676f7b92c2bcd30612295840054ab2f0c0cf12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062217"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-cli"></a>Skapa eller uppdatera anpassade roller för Azure-resurser med Azure CLI

> [!IMPORTANT]
> Att lägga till `AssignableScopes` en hanteringsgrupp i är för närvarande i förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om de [inbyggda rollerna för Azure-resurser](built-in-roles.md) inte uppfyller organisationens specifika krav, kan du skapa egna anpassade roller. I den här artikeln beskrivs hur du listar, skapar, uppdaterar eller tar bort anpassade roller med Azure CLI.

En steg-för-steg-självstudie om hur du skapar en anpassad roll finns i [Självstudiekurs: Skapa en anpassad roll för Azure-resurser med Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Krav

Om du vill skapa anpassade roller behöver du:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)
- [Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa anpassade roller som är tillgängliga för tilldelning använder du [listan över az-rolldefinition](/cli/azure/role/definition#az-role-definition-list). I följande exempel visas alla anpassade roller i den aktuella prenumerationen.

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

## <a name="list-a-custom-role-definition"></a>Lista en anpassad rolldefinition

Om du vill visa en anpassad rolldefinition använder du [en lista över az-rolldefinition](/cli/azure/role/definition#az-role-definition-list). Det här är samma kommando som du skulle använda för en inbyggd roll.

```azurecli
az role definition list --name <role_name>
```

I följande exempel visas rolldefinitionen *för operatör för virtuell dator:*

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

I följande exempel visas bara åtgärderna för rollen *operatör för virtuell dator:*

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

Om du vill skapa en anpassad roll använder du [az-rolldefinition skapa](/cli/azure/role/definition#az-role-definition-create). Rolldefinitionen kan vara en JSON-beskrivning eller en sökväg till en fil som innehåller en JSON-beskrivning.

```azurecli
az role definition create --role-definition <role_definition>
```

I följande exempel skapas en anpassad roll med namnet *Virtual Machine Operator*. Den här anpassade rollen tilldelar åtkomst till alla läsåtgärder för *Microsoft.Compute-,* *Microsoft.Storage-* och *Microsoft.Network-resursleverantörer* och tilldelar åtkomst till start, omstart och övervakar virtuella datorer. Den här anpassade rollen kan användas i två prenumerationer. I det här exemplet används en JSON-fil som indata.

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

Om du vill uppdatera en anpassad roll använder du först [den az-rolldefinitionslistan](/cli/azure/role/definition#az-role-definition-list) för att hämta rolldefinitionen. För det andra gör du önskade ändringar i rolldefinitionen. Slutligen kan du använda uppdatering av [az-rolldefinition](/cli/azure/role/definition#az-role-definition-update) för att spara den uppdaterade rolldefinitionen.

```azurecli
az role definition update --role-definition <role_definition>
```

I följande exempel läggs *åtgärden Microsoft.Insights/diagnosticSettings/* till `Actions` `AssignableScopes` och lägger till en hanteringsgrupp för den anpassade rollen *För operatör* för virtuell dator. Att lägga till `AssignableScopes` en hanteringsgrupp i är för närvarande i förhandsversion.

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
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

Om du vill ta bort en anpassad roll använder du [ta bort az-rolldefinition](/cli/azure/role/definition#az-role-definition-delete). Om du vill ange vilken roll du vill ta bort använder du rollnamnet eller roll-ID:t. Om du vill ta reda på roll-ID:et använder du [listan över az-rolldefinition](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

I följande exempel tas den anpassade rollen *för operatör för virtuell dator.*

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Nästa steg

- [Självstudiekurs: Skapa en anpassad roll för Azure-resurser med Azure CLI](tutorial-custom-role-cli.md)
- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Azure Resource Manager-resursprovideråtgärder](resource-provider-operations.md)