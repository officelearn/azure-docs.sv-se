---
title: Skapa eller uppdatera anpassade roller för Azure-resurser med hjälp av Azure CLI | Microsoft Docs
description: Lär dig att visa, skapa, uppdatera eller ta bort anpassade roller med rollbaserad åtkomst kontroll (RBAC) för Azure-resurser med hjälp av Azure CLI.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062217"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-cli"></a>Skapa eller uppdatera anpassade roller för Azure-resurser med Azure CLI

> [!IMPORTANT]
> Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om de [inbyggda rollerna för Azure-resurser](built-in-roles.md) inte uppfyller organisationens specifika krav, kan du skapa egna anpassade roller. Den här artikeln beskriver hur du visar, skapar, uppdaterar eller tar bort anpassade roller med Azure CLI.

En stegvis själv studie kurs om hur du skapar en anpassad roll finns i [Självstudier: skapa en anpassad roll för Azure-resurser med hjälp av Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Krav

Om du vill skapa anpassade roller behöver du:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)
- [Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa en lista med anpassade roller som är tillgängliga för tilldelning använder du [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list). Följande exempel visar en lista över alla anpassade roller i den aktuella prenumerationen.

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

## <a name="list-a-custom-role-definition"></a>Lista en anpassad roll definition

Om du vill visa en anpassad roll definition använder du [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list). Det här är samma kommando som du använder för en inbyggd roll.

```azurecli
az role definition list --name <role_name>
```

I följande exempel visas roll definitionen för den *virtuella datorns operatör* :

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

I följande exempel visas bara åtgärder för rollen *virtuell dator operatör* :

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

Använd [AZ roll definition Create](/cli/azure/role/definition#az-role-definition-create)för att skapa en anpassad roll. Roll definitionen kan vara en JSON-beskrivning eller en sökväg till en fil som innehåller en JSON-beskrivning.

```azurecli
az role definition create --role-definition <role_definition>
```

I följande exempel skapas en anpassad roll med namnet *Virtual Machine-operator*. Den här anpassade rollen tilldelar åtkomst till alla Läs åtgärder för *Microsoft. Compute*, *Microsoft. Storage*och *Microsoft. Network* Resource providers och tilldelar åtkomst till Start, omstart och övervakning av virtuella datorer. Den här anpassade rollen kan användas i två prenumerationer. I det här exemplet används en JSON-fil som indata.

vmoperator. JSON

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

Om du vill uppdatera en anpassad roll ska du först använda [AZ Role definition List](/cli/azure/role/definition#az-role-definition-list) för att hämta roll definitionen. Sedan gör du önskade ändringar i roll definitionen. Använd slutligen [AZ roll definitions uppdatering](/cli/azure/role/definition#az-role-definition-update) för att spara den uppdaterade roll definitionen.

```azurecli
az role definition update --role-definition <role_definition>
```

I följande exempel läggs *Microsoft. Insights/diagnosticSettings/* operation till `Actions` och lägger till en hanterings grupp till `AssignableScopes` för den anpassade rollen för den *virtuella datorn* . Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.

vmoperator. JSON

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

Om du vill ta bort en anpassad roll använder du [AZ roll definition Delete](/cli/azure/role/definition#az-role-definition-delete). Om du vill ange vilken roll som ska tas bort använder du roll namnet eller roll-ID: t. Använd [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list)för att fastställa roll-ID: t.

```azurecli
az role definition delete --name <role_name or role_id>
```

I följande exempel tar vi bort den anpassade rollen för den *virtuella datorn* .

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa en anpassad roll för Azure-resurser med hjälp av Azure CLI](tutorial-custom-role-cli.md)
- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Åtgärder för Azure Resource Manager Resource Provider](resource-provider-operations.md)