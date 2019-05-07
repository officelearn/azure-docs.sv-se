---
title: Använda Azure CLI för att hantera Azure AD-åtkomsträttigheter till blob och kö data med RBAC - Azure Storage
description: Använda Azure CLI för att tilldela åtkomst till behållare och köer med rollbaserad åtkomstkontroll (RBAC). Azure Storage stöder inbyggda och anpassade RBAC-roller för autentisering via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: dc2beda1ae017b5e81fddf08d0c7e88c785bcdf5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153885"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-azure-cli"></a>Bevilja åtkomst till Azure blob och kö data med RBAC med Azure CLI

Azure Active Directory (Azure AD) auktoriserar åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar av behörigheter som används för att komma åt data blob eller kön. 

När en RBAC-roll tilldelas till en Azure AD-säkerhetsobjekt, Azure beviljar åtkomst till att dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till nivån för prenumerationen, resursgruppen, storage-konto eller en enskild behållare eller en kö. En Azure AD-säkerhetsobjekt kan vara en användare, en grupp, tjänstens huvudnamn för ett program eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Den här artikeln beskriver hur du använder Azure CLI för att visa en lista över inbyggda RBAC-roller och tilldela dem till användare. Mer information om hur du använder Azure CLI finns i [Azure kommandoradsgränssnitt (CLI)](https://docs.microsoft.com/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Bestäm resource omfattning 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Lista tillgängliga RBAC-roller

Om du vill visa tillgängliga inbyggda RBAC-roller med Azure CLI, använder den [az role definition list](/cli/azure/role/definition#az-role-definition-list) kommando:

```azurecli-interactive
az role definition list --out table
```

Du ser de inbyggda Azure Storage data roller visas, tillsammans med andra inbyggda roller för Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>Tilldela en RBAC-roll till en användare

Om du vill tilldela en RBAC-roll till en användare använder den [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) kommando. Formatet för kommandot kan variera beroende på omfånget för tilldelningen. I följande exempel visas hur du tilldela en roll till en användare på olika omfång.

### <a name="container-scope"></a>Behållaren omfång

Tilldela en roll som är begränsade till en behållare genom att ange en sträng som innehåller omfånget för behållaren för den `--scope` parametern. Omfattningen för en behållare är i formatet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

I följande exempel tilldelas den **Storage Blob Data-deltagare** roll till en användare som hör till en behållare med namnet *exempelbehållaren*. Se till att ersätta exempelvärdena och du platshållarens värden inom hakparentes med dina egna värden: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Köomfång

Om du vill tilldela en roll som är begränsade till en kö, ange en sträng som innehåller omfånget för kön för den `--scope` parametern. Omfattningen för en kö är i formatet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

I följande exempel tilldelas den **Lagringsködata-deltagare** roll till en användare som hör till en kö med namnet *exempel kön*. Se till att ersätta exempelvärdena och du platshållarens värden inom hakparentes med dina egna värden: 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Storage-konto omfång

Tilldela en roll som är begränsade till lagringskontot genom att ange omfånget för resursen för lagringskonton för den `--scope` parametern. Omfånget för ett lagringskonto är i formatet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

I följande exempel visar hur du tilldelar den **Storage Blob Data-läsare** roll till en användare på nivån för storage-konto. Se till att ersätta exempelvärdena med dina egna värden: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/storagesamples"
```

### <a name="resource-group-scope"></a>Resursen Gruppomfång

Om du vill tilldela en roll som är begränsade till resursgruppen, ange resursgruppens namn eller ID: T för den `--resource-group` parametern. I följande exempel tilldelas den **Lagringsködata-läsare** roll till en användare på nivån för resursgruppen. Se till att ersätta exempelvärden och platshållarens värden inom hakparentes med dina egna värden: 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group sample-resource-group
```

### <a name="subscription-scope"></a>Prenumerationsomfattningen

Tilldela en roll som hör till prenumerationen genom att ange omfånget för prenumerationen för den `--scope` parametern. Omfattningen för en prenumeration är i formatet:

```
/subscriptions/<subscription>
```

I följande exempel visar hur du tilldelar den **Storage Blob Data-läsare** roll till en användare på nivån för storage-konto. Se till att ersätta exempelvärdena med dina egna värden: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>"
```

## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomst till Azure-resurser med hjälp av RBAC och Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Bevilja åtkomst till Azure blob och kö data med RBAC med Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Bevilja åtkomst till Azure blob och kö data med RBAC i Azure portal](storage-auth-aad-rbac-portal.md)
