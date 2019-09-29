---
title: Använd Azure PowerShell för att hantera Azure AD-åtkomst rättigheter till blob-och Queue-data med RBAC-Azure Storage
description: Använd Azure PowerShell för att tilldela åtkomst till behållare och köer med rollbaserad åtkomst kontroll (RBAC). Azure Storage stöder inbyggda och anpassade RBAC-roller för autentisering via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 967e1754ec4be504669e176a5643186d08efb9d4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673180"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Bevilja åtkomst till Azure blob-och Queue-data med RBAC med hjälp av PowerShell

Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll (RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar behörigheter som används för att komma åt behållare eller köer. 

När en RBAC-roll tilldelas till ett säkerhets objekt i Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen, lagrings kontot eller en enskild behållare eller kö. Ett säkerhets objekt i Azure AD kan vara en användare, en grupp, ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Den här artikeln beskriver hur du använder Azure PowerShell för att Visa inbyggda RBAC-roller och tilldela dem till användare. Mer information om hur du använder Azure PowerShell finns i [Översikt över Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Bestäm resurs omfång

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Lista tillgängliga RBAC-roller

Om du vill visa en lista över tillgängliga, Inbyggda RBAC-roller med Azure PowerShell, använder du kommandot [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) :

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Du ser de inbyggda Azure Storage data rollerna i listan, tillsammans med andra inbyggda roller för Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Tilldela en RBAC-roll till ett säkerhets objekt

Använd kommandot [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) om du vill tilldela en RBAC-roll till ett säkerhets objekt. Kommandots format kan variera beroende på tilldelningens omfattning. I följande exempel visas hur du tilldelar en roll till en användare i olika scope, men du kan använda samma kommando för att tilldela en roll till alla säkerhets objekt.

### <a name="container-scope"></a>Container omfång

Om du vill tilldela en roll begränsad till en behållare anger du en sträng som innehåller omfånget för behållaren för parametern `--scope`. Omfånget för en behållare är i formatet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

I följande exempel tilldelas rollen **Storage BLOB data Contributor** till en användare som är begränsad till en behållare med namnet *Sample-container*. Se till att ersätta exempel värdena och plats hållarna inom hakparenteser med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Köa omfång

Om du vill tilldela en roll som är begränsad till en kö anger du en sträng som innehåller omfånget för kön för parametern `--scope`. Omfattningen för en kö är i formatet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

I följande exempel tilldelas rollen **Storage Queue data Contributor** till en användare som är begränsad till en kö med namnet *exempel-Queue*. Se till att ersätta exempel värdena och plats hållarna inom hakparenteser med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Lagrings konto omfång

Om du vill tilldela en roll som är begränsad till lagrings kontot anger du omfånget för lagrings konto resursen för parametern `--scope`. Omfånget för ett lagrings konto har formatet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

I följande exempel visas hur du omfångerar rollen **Storage BLOB data Reader** till en användare på lagrings kontots nivå. Se till att ersätta exempel värdena med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Definitions område för resurs grupp

Om du vill tilldela en roll som är begränsad till resurs gruppen anger du resurs gruppens namn eller ID för parametern `--resource-group`. I följande exempel tilldelas rollen **data läsare för lagrings köer** till en användare på nivån i resurs gruppen. Ersätt värdena för exempel värden och plats hållare inom hak paren tes med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Prenumerations omfång

Om du vill tilldela en roll som är begränsad till prenumerationen anger du omfånget för prenumerationen för parametern `--scope`. Omfånget för en prenumeration är i formatet:

```
/subscriptions/<subscription>
```

I följande exempel visas hur du tilldelar rollen **Storage BLOB data Reader** till en användare på lagrings kontots nivå. Se till att ersätta exempel värdena med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomst till Azure-resurser med hjälp av RBAC och Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Bevilja åtkomst till blob- och ködata i Azure med RBAC med hjälp av Azure CLI](storage-auth-aad-rbac-cli.md)
- [Bevilja åtkomst till blob- och ködata i Azure med RBAC på Azure-portalen](storage-auth-aad-rbac-portal.md)
