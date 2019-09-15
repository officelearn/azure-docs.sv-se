---
title: Hantera åtkomst till Azure-resurser med RBAC och Azure CLI | Microsoft Docs
description: Lär dig hur du hanterar åtkomst till Azure-resurser för användare, grupper och program med rollbaserad åtkomst kontroll (RBAC) och Azure CLI. Detta innefattar hur du listar åtkomst, ger åtkomst och tar bort åtkomst.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1b898f42fa6f66fba7c84daa67769249642bd986
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996487"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Hantera åtkomst till Azure-resurser med RBAC och Azure CLI

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är metoden som du använder när du hanterar åtkomst till Azure-resurser. Den här artikeln beskriver hur du hanterar åtkomst för användare, grupper och program med RBAC och Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

Om du vill hantera åtkomst behöver du något av följande:

* [Bash i Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>Visa roller

Om du vill visa en lista över alla tillgängliga roll definitioner använder du [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

I följande exempel visas namn och beskrivning för alla tillgängliga roll definitioner:

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

I följande exempel visas alla inbyggda roll definitioner:

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

## <a name="list-a-role-definition"></a>Lista en roll definition

Om du vill visa en roll definition använder du [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

I följande exempel visas *deltagar* roll definitionen:

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

### <a name="list-actions-of-a-role"></a>Lista åtgärder för en roll

I följande exempel visas bara *åtgärder* och *notActions* för *deltagar* rollen:

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

I följande exempel visas bara åtgärder för rollen *virtuell dator deltagare* :

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

I RBAC, för att lista åtkomst, listas roll tilldelningarna.

### <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

Om du vill visa roll tilldelningarna för en speciell användare använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Som standard visas endast direkta tilldelningar som är begränsade till prenumerationen. Om du vill visa tilldelningar som omfattas av resurs eller `--all` grupp, använder du och för att `--include-inherited`Visa ärvda tilldelningar.

I följande exempel visas roll tilldelningarna som tilldelas direkt till *patlong\@contoso.com* -användaren:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Lista roll tilldelningar i ett resurs grupps omfång

Om du vill visa en lista över roll tilldelningar som finns i ett resurs grupps omfång använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

I följande exempel visas roll tilldelningarna för resurs gruppen *Pharma-Sales* :

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>Lista roll tilldelningar i ett prenumerations omfång

Om du vill visa en lista över alla roll tilldelningar i ett prenumerations omfång använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list). Om du vill hämta prenumerations-ID: t kan du hitta det på bladet **prenumerationer** i Azure Portal eller så kan du använda [konto listan AZ](/cli/azure/account#az-account-list).

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Lista roll tilldelningar i ett hanterings grupps omfång

Om du vill visa alla roll tilldelningar i ett hanterings grupps omfång använder du [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list). Om du vill hämta ID för hanterings grupp kan du hitta det på bladet **hanterings grupper** i Azure Portal eller så kan du använda [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="grant-access"></a>Bevilja åtkomst

För att skapa åtkomst i RBAC skapar du rolltilldelningar.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Skapa en roll tilldelning för en användare i ett resurs grupps omfång

Om du vill bevilja åtkomst till en användare i ett resurs grupps omfång använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

I följande exempel tilldelas rollen *virtuell dator deltagare* till *\@patlong contoso.com* -användare i resurs grupps omfånget *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Skapa en roll tilldelning med hjälp av unikt roll-ID

Det finns ett par gånger när ett roll namn kan ändras, till exempel:

- Du använder din egen anpassade roll och du bestämmer dig för att ändra namnet.
- Du använder en förhands gransknings roll som har **(förhands granskning)** i namnet. När rollen släpps får rollen ett nytt namn.

> [!IMPORTANT]
> En för hands version tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Även om en roll får ett nytt namn ändras inte roll-ID: t. Om du använder skript eller automatisering för att skapa roll tilldelningar, är det en bra idé att använda det unika roll-ID: t i stället för roll namnet. Därför är skripten mer sannolika om du byter namn på en roll.

Om du vill skapa en roll tilldelning med det unika roll-ID: t i stället för roll namnet använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

I följande exempel tilldelas rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till *\@patlong contoso.com* -användaren i resurs grupps omfånget *Pharma-Sales* . Om du vill hämta det unika roll-ID: t kan du använda [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list) eller se [inbyggda roller för Azure-resurser](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Skapa en roll tilldelning för en grupp

Om du vill bevilja åtkomst till en grupp använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Om du vill hämta ID för gruppen kan du använda [AZ AD Group List](/cli/azure/ad/group#az-ad-group-list) eller [AZ AD Group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

I följande exempel tilldelas rollen *Reader* till den *Ann Mack team* gruppen med ID 22222222-2222-2222-2222-222222222222 i ett prenumerations omfång.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

I följande exempel tilldelas rollen *virtuell dator deltagare* till den *Ann Mack team* gruppen med ID 22222222-2222-2222-2222-222222222222 i ett resurs omfång för ett virtuellt nätverk med namnet *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>Skapa en roll tilldelning för ett program i ett resurs grupps omfång

Om du vill bevilja åtkomst till ett program använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Om du vill hämta objekt-ID för programmet kan du använda [AZ AD App List](/cli/azure/ad/app#az-ad-app-list) eller [AZ AD App show](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

I följande exempel tilldelas rollen *virtuell dator deltagare* till ett program med objekt-ID 44444444-4444-4444-4444-444444444444 i resurs grupps omfånget *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>Skapa en roll tilldelning för en användare med ett prenumerations omfång

Om du vill bevilja åtkomst till en användare i ett prenumerations omfång använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Om du vill hämta prenumerations-ID: t kan du hitta det på bladet **prenumerationer** i Azure Portal eller så kan du använda [konto listan AZ](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

I följande exempel tilldelas *läsaren* rollen till *annm\@example.com* -användaren i ett prenumerations omfång.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Skapa en roll tilldelning för en användare i en hanterings grupps omfattning

Om du vill bevilja åtkomst till en användare i ett hanterings grupps omfång använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create). Om du vill hämta ID för hanterings grupp kan du hitta det på bladet **hanterings grupper** i Azure Portal eller så kan du använda [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

I följande exempel tilldelar du rollen för *fakturerings läsaren* till *\@Alain example.com* -användaren i en hanterings grupps definitions område.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="remove-access"></a>Ta bort åtkomst

I RBAC för att ta bort åtkomst tar du bort en roll tilldelning genom att använda [AZ roll tilldelning ta bort](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

I följande exempel tas roll tilldelningen för *virtuell dator deltagare* bort *från\@patlong contoso.com* -användaren på resurs gruppen *Pharma-Sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

I följande exempel tas rollen *läsare* bort från *Ann Mack-team* gruppen med ID 22222222-2222-2222-2222-222222222222 i ett prenumerations omfång. Om du vill hämta ID för gruppen kan du använda [AZ AD Group List](/cli/azure/ad/group#az-ad-group-list) eller [AZ AD Group show](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

I följande exempel tas rollen för *fakturerings läsaren* bort från *Alain\@example.com* -användaren i hanterings gruppens omfång. Om du vill hämta ID för hanterings gruppen kan du använda [AZ Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Nästa steg

- [Självstudier: Skapa en anpassad roll för Azure-resurser med hjälp av Azure CLI](tutorial-custom-role-cli.md)
- [Använd Azure CLI för att hantera Azure-resurser och resurs grupper](../azure-resource-manager/cli-azure-resource-manager.md)
