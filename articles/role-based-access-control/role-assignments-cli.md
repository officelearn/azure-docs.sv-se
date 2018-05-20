---
title: Hantera rollbaserad åtkomstkontroll (RBAC) med Azure CLI | Microsoft Docs
description: Lär dig mer om att hantera rollbaserad åtkomstkontroll (RBAC) med kommandoradsgränssnittet i Azure genom att ange roller och rollen åtgärder och genom att tilldela roller till prenumeration och application-scope.
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
ms.date: 04/03/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 4a88f78f1f3fc1eaf8d6f9beae42119fe42f1807
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Hantera rollbaserad åtkomstkontroll med kommandoradsgränssnittet i Azure

> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [Azure CLI](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)


Med rollbaserad åtkomstkontroll (RBAC) ange åtkomst för användare, grupper och tjänstens huvudnamn genom att tilldela roller för ett visst område. Den här artikeln beskriver hur du hanterar rolltilldelningar med hjälp av Azure-kommandoradsgränssnittet (CLI).

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure CLI för att hantera rolltilldelningar, måste du ha följande krav:

* [Azure CLI 2.0](/cli/azure). Du kan använda den i din webbläsare med [Azure Cloud Shell](../cloud-shell/overview.md) eller [installera](/cli/azure/install-azure-cli) den på macOS, Linux och Windows och köra den från kommandoraden.

## <a name="list-role-definitions"></a>Lista rolldefinitioner

Om du vill visa alla tillgängliga rolldefinitioner använda [az rollen definitionslista](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

I följande exempel visar namn och beskrivning av alla tillgängliga rolldefinitioner:

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

I följande exempel visar alla de inbyggda rolldefinitioner:

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

### <a name="list-actions-of-a-role-definition"></a>Lista över åtgärder som en rolldefinition

Visa åtgärderna för en rolldefinition, Använd [az rollen definitionslista](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

I följande exempel visas den *deltagare* rolldefinitionen:

```azurecli
az role definition list --name "Contributor"
```

```Output
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

I följande exempel visas den *åtgärder* och *notActions* av den *deltagare* roll:

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

I följande exempel visar åtgärder för den *Virtual Machine-deltagare* roll:

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

## <a name="list-role-assignments"></a>Lista rolltilldelningar

### <a name="list-role-assignments-for-a-user"></a>Lista rolltilldelningar för en användare

Om du vill visa rolltilldelningar för en viss användare, Använd [az rollen tilldelningslista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Som standard visas endast tilldelningar som är begränsade till prenumerationen. Du kan visa tilldelningar begränsas per resurs eller grupp `--all`.

I följande exempel visar rolltilldelningar som tilldelas direkt till den *patlong@contoso.com* användare:

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

### <a name="list-role-assignments-for-a-resource-group"></a>Lista rolltilldelningar för en resursgrupp

Om du vill visa rolltilldelningar som finns för en resursgrupp, Använd [az rollen tilldelningslista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

I följande exempel visar rolltilldelningar för den *pharma-försäljning-projectforecast* resursgrupp:

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

## <a name="create-role-assignments"></a>Skapa rolltilldelningar

### <a name="create-a-role-assignment-for-a-user"></a>Skapa en rolltilldelning för en användare

Så här skapar du en rolltilldelning för en användare på resursen Gruppomfång [az rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

I följande exempel tilldelas den *Virtual Machine-deltagare* roll *patlong@contoso.com* användare på den *pharma-försäljning-projectforecast* resurs Gruppomfång:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>Skapa en rolltilldelning för en grupp

Du kan skapa en rolltilldelning för en grupp med [az rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

I följande exempel tilldelas den *Reader* rollen till den *Ann Mack Team* med ID 22222222-2222-2222-2222-222222222222 definitionsområdet prenumeration. Du kan använda för att hämta ID för gruppen [az ad grupplistan](/cli/azure/ad/group#az-ad-group-list) eller [az ad-gruppen visa](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

I följande exempel tilldelas den *Virtual Machine-deltagare* rollen till den *Ann Mack Team* med ID 22222222-2222-2222-2222-222222222222 definitionsområdet resurs för ett virtuellt nätverk med namnet *pharma-försäljning-projekt-network*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Skapa en rolltilldelning för ett program

Du kan skapa en roll för ett program med [az rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

I följande exempel tilldelas den *Virtual Machine-deltagare* rollen till ett program med objekt-ID 44444444-4444-4444-4444-444444444444 på den *pharma-försäljning-projectforecast* resursgruppen. omfattningen. Du kan använda för att få objekt-ID för programmet, [az ad app-lista](/cli/azure/ad/app#az-ad-app-list) eller [az ad app visa](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

Ta bort en rolltilldelning med [az rolltilldelning ta bort](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

I följande exempel tar bort den *Virtual Machine-deltagare* rolltilldelningen från den *patlong@contoso.com* användare på den *pharma-försäljning-projectforecast* resurs grupp:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

I följande exempel tar bort den *Reader* roll från den *Ann Mack Team* med ID 22222222-2222-2222-2222-222222222222 definitionsområdet prenumeration. Du kan använda för att hämta ID för gruppen [az ad grupplistan](/cli/azure/ad/group#az-ad-group-list) eller [az ad-gruppen visa](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>Anpassade roller

### <a name="list-custom-roles"></a>Lista över anpassade roller

Om du vill visa de roller som är tillgängliga för tilldelning på scopenivå, använder [az rollen definitionslista](/cli/azure/role/definition#az-role-definition-list).

Båda av följande exempel lista alla anpassade roller i den aktuella prenumerationen:

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
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>Skapa en anpassad roll

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

### <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

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

### <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

Ta bort en anpassad roll genom att använda [az rolldefinitionen ta bort](/cli/azure/role/definition#az-role-definition-delete). Rollen för att ta bort Använd rollnamnet eller roll-ID. Använd för att fastställa roll-ID [az rollen definitionslista](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

I följande exempel tar bort den *virtuella operatorn* anpassad roll:

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

