---
title: Använda PowerShell för att tilldela en RBAC-roll för dataåtkomst
titleSuffix: Azure Storage
description: Lär dig hur du använder PowerShell för att tilldela behörigheter till ett Azure Active Directory-säkerhetsobjekt med rollbaserad åtkomstkontroll (RBAC). Azure Storage stöder inbyggda och anpassade RBAC-roller för autentisering via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1413035c879198cf333aeeb5d8fe993162939172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460581"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Använda PowerShell för att tilldela en RBAC-roll för åtkomst till blob- och ködata

Azure Active Directory (Azure AD) godkänner åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar med behörigheter som används för att komma åt behållare eller köer.

När en RBAC-roll tilldelas ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till prenumerationsnivån, resursgruppen, lagringskontot eller en enskild behållare eller kö. Ett Azure AD-säkerhetsobjekt kan vara en användare, en grupp, ett programtjänsthuvudnamn eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

I den här artikeln beskrivs hur du använder Azure PowerShell för att lista inbyggda RBAC-roller och tilldela dem till användare. Mer information om hur du använder Azure PowerShell finns i [Översikt över Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Fastställ resursomfattning

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Lista tillgängliga RBAC-roller

Om du vill visa tillgängliga inbyggda RBAC-roller med Azure PowerShell använder du kommandot [Get-AzRoleDefinition:](/powershell/module/az.resources/get-azroledefinition)

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Du ser de inbyggda Azure Storage-datarollerna i listan tillsammans med andra inbyggda roller för Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Tilldela en RBAC-roll till ett säkerhetsobjekt

Om du vill tilldela en RBAC-roll till ett säkerhetsobjekt använder du budet [Ny-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment) Kommandots format kan skilja sig beroende på tilldelningens omfattning. För att kunna köra kommandot måste du ha rollen Ägare eller Deltagare tilldelad i motsvarande omfång. Följande exempel visar hur du tilldelar en roll till en användare i olika scope, men du kan använda samma kommando för att tilldela en roll till alla säkerhetsobjekt.

### <a name="container-scope"></a>Behållare omfattning

Om du vill tilldela en roll som omfattas till en behållare `--scope` anger du en sträng som innehåller behållarens omfattning för parametern. Omfånget för en behållare finns i formuläret:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

I följande exempel tilldelas rollen **Storage Blob Data Contributor** till en användare, begränsad till en behållare med namnet *sample-container*. Se till att ersätta exempelvärdena och platshållarvärdena inom parentes med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Köomfattning

Om du vill tilldela en roll som är begränsad till en `--scope` kö anger du en sträng som innehåller omfånget för kön för parametern. Omfånget för en kö finns i formuläret:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

I följande exempel tilldelas rollen **Storage Queue Data Contributor** till en användare, begränsad till en kö med namnet *exempelkö*. Se till att ersätta exempelvärdena och platshållarvärdena inom parentes med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Omfattning för lagringskonto

Om du vill tilldela en roll som omfattas av lagringskontot anger du omfattningen av lagringskontoresursen för parametern. `--scope` Omfattningen för ett lagringskonto finns i formuläret:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

I följande exempel visas hur du scopear rollen **Storage Blob Data Reader** till en användare på lagringskontots nivå. Se till att ersätta exempelvärdena med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Resursgruppomfång

Om du vill tilldela en roll som är begränsad till resursgruppen anger du resursgruppsnamnet eller ID:t för parametern. `--resource-group` I följande exempel tilldelas rollen **Lagringsködataläsare** till en användare på resursgruppens nivå. Se till att ersätta exempelvärdena och platshållarvärdena inom parentes med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Prenumerationsscope

Om du vill tilldela en roll som omfattas av `--scope` prenumerationen anger du omfånget för prenumerationen för parametern. Omfattningen för en prenumeration finns i formuläret:

```
/subscriptions/<subscription>
```

I följande exempel visas hur du tilldelar rollen **Storage Blob Data Reader** till en användare på lagringskontots nivå. Se till att ersätta exempelvärdena med dina egna värden: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Nästa steg

- [Hantera åtkomst till Azure-resurser med hjälp av RBAC och Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Bevilja åtkomst till blob- och ködata i Azure med RBAC med hjälp av Azure CLI](storage-auth-aad-rbac-cli.md)
- [Bevilja åtkomst till blob- och ködata i Azure med RBAC på Azure-portalen](storage-auth-aad-rbac-portal.md)
