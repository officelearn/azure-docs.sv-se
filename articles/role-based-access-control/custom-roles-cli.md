---
title: Skapa anpassade roller med hjälp av Azure CLI | Microsoft Docs
description: Lär dig hur du skapar anpassade roller för rollbaserad åtkomstkontroll (RBAC) med hjälp av Azure CLI. Detta innefattar hur du visa, skapa, uppdatera och ta bort anpassade roller.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b1df50c73497e3f5ad64a7ba7210079871b155e0
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321159"
---
# <a name="create-custom-roles-using-azure-cli"></a>Skapa anpassade roller med hjälp av Azure CLI

Om den [inbyggda roller](built-in-roles.md) inte uppfyller de specifika behoven i din organisation kan du skapa egna anpassade roller. Den här artikeln beskriver hur du skapar och hanterar anpassade roller med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa anpassade roller, behöver du:

- Behörighet att skapa anpassade roller som [ägare](built-in-roles.md#owner) eller [administratör för användaråtkomst](built-in-roles.md#user-access-administrator)
- [Azure CLI](/cli/azure/install-azure-cli) installeras lokalt

## <a name="list-custom-roles"></a>Lista över anpassade roller

Om du vill visa en lista med anpassade roller som är tillgängliga för tilldelning, Använd [az rollen definitionslista](/cli/azure/role/definition#az-role-definition-list). Följande lista alla anpassade roller i den aktuella prenumerationen.

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

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Så här skapar du en anpassad roll [az rolldefinitionen skapa](/cli/azure/role/definition#az-role-definition-create). Rolldefinitionen kan vara en JSON-beskrivning eller en sökväg till en fil som innehåller en JSON-beskrivning.

```azurecli
az role definition create --role-definition <role_definition>
```

I följande exempel skapas en anpassad roll med namnet *virtuella operatorn*. Den här anpassade rollen tilldelar åtkomst till alla läsåtgärder av *Microsoft.Compute*, *Microsoft.Storage*, och *Microsoft.Network* providers och tilldelar resursåtkomst Om du vill starta, starta om och övervaka virtuella datorer. Den här anpassade rollen kan användas i två prenumerationer. Det här exemplet används en JSON-fil som indata.

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

Om du vill uppdatera en anpassad roll först använda [az rollen definitionslista](/cli/azure/role/definition#az-role-definition-list) att hämta rolldefinitionen. Andra, gör ändringarna i rolldefinitionen. Använd slutligen [az rollen Definitionsuppdatering](/cli/azure/role/definition#az-role-definition-update) att spara den uppdaterade rolldefinitionen.

```azurecli
az role definition update --role-definition <role_definition>
```

I följande exempel läggs den *Microsoft.Insights/diagnosticSettings/* åtgärden den *åtgärder* av den *virtuella operatorn* anpassad roll.

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

Ta bort en anpassad roll genom att använda [az rolldefinitionen ta bort](/cli/azure/role/definition#az-role-definition-delete). Rollen för att ta bort Använd rollnamnet eller roll-ID. Använd för att fastställa roll-ID [az rollen definitionslista](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

I följande exempel tar bort den *virtuella operatorn* anpassad roll.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Nästa steg

- [Självstudier: Skapa en anpassad roll med hjälp av Azure CLI](tutorial-custom-role-cli.md)
- [Anpassade roller i Azure](custom-roles.md)
- [Provideråtgärder i Azure Resource Manager resurs](resource-provider-operations.md)