---
title: Använd PowerShell för att tilldela en Azure-roll för data åtkomst
titleSuffix: Azure Storage
description: Lär dig hur du använder PowerShell för att tilldela behörigheter till ett Azure Active Directory säkerhets objekt med rollbaserad åtkomst kontroll (RBAC). Azure Storage stöder inbyggda och Azure-anpassade roller för autentisering via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 17a34ac3017ee08626dfc17325c29b43b34b5505
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827970"
---
# <a name="use-powershell-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Använd PowerShell för att tilldela en Azure-roll för åtkomst till blob-och Queue-data

Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättningar behörigheter som används för att komma åt behållare eller köer.

När en Azure-roll tilldelas till ett säkerhets objekt för Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen, lagrings kontot eller en enskild behållare eller kö. Ett säkerhets objekt i Azure AD kan vara en användare, en grupp, ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Den här artikeln beskriver hur du använder Azure PowerShell för att visa en lista över inbyggda Azure-roller och tilldela dem till användare. Mer information om hur du använder Azure PowerShell finns i [Översikt över Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-roles-for-blobs-and-queues"></a>Azure-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Bestäm resurs omfång

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Lista tillgängliga Azure-roller

Om du vill visa en lista över tillgängliga Azure inbyggda roller med Azure PowerShell använder du kommandot [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) :

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

## <a name="assign-an-azure-role-to-a-security-principal"></a>Tilldela en Azure-roll till ett säkerhets objekt

Använd kommandot [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) om du vill tilldela en Azure-roll till ett säkerhets objekt. Kommandots format kan variera beroende på tilldelningens omfattning. För att kunna köra kommandot måste du ha ägar-eller deltagar rollen tilldelad till motsvarande omfång. I följande exempel visas hur du tilldelar en roll till en användare i olika scope, men du kan använda samma kommando för att tilldela en roll till alla säkerhets objekt.

### <a name="container-scope"></a>Container omfång

Om du vill tilldela en roll begränsad till en behållare anger du en sträng som innehåller omfånget för `--scope` parametern. Omfånget för en behållare är i formatet:

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

Om du vill tilldela en roll som är begränsad till en kö anger du en sträng som innehåller omfånget för- `--scope` parametern. Omfattningen för en kö är i formatet:

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

Om du vill tilldela en roll som är begränsad till lagrings kontot anger du omfånget för lagrings konto resursen för `--scope` parametern. Omfånget för ett lagrings konto har formatet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

I följande exempel visas hur du omfångerar rollen **Storage BLOB data Reader** till en användare på lagrings kontots nivå. Se till att ersätta exempel värdena med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Omfånget för resursgrupp

Om du vill tilldela en roll som är begränsad till resurs gruppen anger du resurs gruppens namn eller ID för `--resource-group` parametern. I följande exempel tilldelas rollen **data läsare för lagrings köer** till en användare på nivån i resurs gruppen. Ersätt värdena för exempel värden och plats hållare inom hak paren tes med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Prenumerations omfång

Om du vill tilldela en roll som är begränsad till prenumerationen anger du omfånget för `--scope` parameterns prenumeration. Omfånget för en prenumeration är i formatet:

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
