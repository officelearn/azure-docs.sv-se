---
title: Lista rolltilldelningar med Azure RBAC och Azure CLI
description: Lär dig hur du tar reda på vilka resurser användare, grupper, tjänsthuvudnamn eller hanterade identiteter har åtkomst till med hjälp av Azure-rollbaserad åtkomstkontroll (RBAC) och Azure CLI.
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385069"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Lista rolltilldelningar med Azure RBAC och Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]I den här artikeln beskrivs hur du listar rolltilldelningar med Azure CLI.

> [!NOTE]
> Om din organisation har lagt ut hanteringsfunktioner på entreprenad till en tjänsteleverantör som använder [Azure-delegerad resurshantering](../lighthouse/concepts/azure-delegated-resource-management.md)visas inte rolltilldelningar som auktoriserats av den tjänsteleverantören här.

## <a name="prerequisites"></a>Krav

- [Bash i Azure Cloud Shell](/azure/cloud-shell/overview) eller Azure [CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

Om du vill visa rolltilldelningar för en viss användare använder du [listan över tilldelningar av az-roller:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Som standard visas endast rolltilldelningar för den aktuella prenumerationen. Om du vill visa rolltilldelningar för `--all` den aktuella prenumerationen och nedan lägger du till parametern. Om du vill visa ärvda rolltilldelningar lägger du till parametern. `--include-inherited`

I följande exempel visas de rolltilldelningar som tilldelas direkt till den *contoso.com\@* användaren:

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

## <a name="list-role-assignments-for-a-resource-group"></a>Visa rolltilldelningar för en resursgrupp

Om du vill visa de rolltilldelningar som finns i ett resursgruppomfång använder du [listan över az-rolltilldelning:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

I följande exempel visas rolltilldelningarna för resursgruppen *för läkemedelsförsäljning:*

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

## <a name="list-role-assignments-for-a-subscription"></a>Lista med rolltilldelningar för en prenumeration

Om du vill visa alla rolltilldelningar i ett prenumerationsomfång använder du [listan över tilldelningar av az-roller](/cli/azure/role/assignment#az-role-assignment-list). För att få prenumerations-ID:t hittar du det på **prenumerationsbladet** i Azure-portalen eller så kan du använda [az-kontolistan](/cli/azure/account#az-account-list).

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Exempel:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Lista rolltilldelningar för en hanteringsgrupp

Om du vill visa alla rolltilldelningar i ett hanteringsgruppomfång använder du [listan över tilldelning av az-roller](/cli/azure/role/assignment#az-role-assignment-list). Om du vill hämta hanteringsgrupp-ID:t hittar du det på bladet **Hanteringsgrupper** i Azure-portalen eller så kan du använda [listan över az-kontohanteringsgrupper](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Exempel:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Lista rolltilldelningar för en hanterad identitet

1. Hämta objekt-ID:t för den systemtilldelade eller användartilldelade hanterade identiteten.

    Om du vill hämta objekt-ID:t för en användartilldelad hanterad identitet kan du använda [az ad sp-listan](/cli/azure/ad/sp#az-ad-sp-list) eller [az identity list](/cli/azure/identity#az-identity-list).

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Om du vill hämta objekt-ID:t för en systemtilldelad hanterad identitet kan du använda [az ad sp-listan](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Om du vill visa rolltilldelningarna använder du [listan över tilldelningar av az-roller](/cli/azure/role/assignment#az-role-assignment-list).

    Som standard visas endast rolltilldelningar för den aktuella prenumerationen. Om du vill visa rolltilldelningar för `--all` den aktuella prenumerationen och nedan lägger du till parametern. Om du vill visa ärvda rolltilldelningar lägger du till parametern. `--include-inherited`

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort rolltilldelningar med Azure RBAC och Azure CLI](role-assignments-cli.md)
