---
title: Definiera en lagrad åtkomstprincip med .NET - Azure Storage
description: Lär dig hur du definierar en lagrad åtkomstprincip med hjälp av .NET-klientbiblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990746"
---
# <a name="define-a-stored-access-policy-with-net"></a>Definiera en lagrad åtkomstprincip med .NET

En lagrad åtkomstprincip ger ytterligare en nivå av kontroll över SAS -signaturer (Shared Access) på serversidan. Att definiera en princip för lagrad åtkomst används för att gruppera signaturer för delad åtkomst och ge ytterligare begränsningar för signaturer för delad åtkomst som är bundna av principen. Du kan använda en lagrad åtkomstprincip för att ändra starttid, utgångstid eller behörigheter för en SAS, eller för att återkalla den när den har utfärdats.
  
 Följande lagringsresurser stöder principer för lagrad åtkomst:  
  
- Blob-containrar  
- Filresurser  
- Köer  
- Tabeller  
  
> [!NOTE]
> En lagrad åtkomstprincip på en behållare kan associeras med en delad åtkomstsignatur som ger behörighet till själva behållaren eller till de blobbar som den innehåller. På samma sätt kan en lagrad åtkomstprincip för en filresurs associeras med en signatur med delad åtkomst som ger behörighet till själva resursen eller till de filer den innehåller.  
>
> Principer för lagrad åtkomst stöds endast för en tjänst SAS. Principer för lagrad åtkomst stöds inte för SAS- eller användardelegerings SAS för konto.  

## <a name="create-a-stored-access-policy"></a>Skapa en lagrad åtkomstprincip

Följande kod skapar en lagrad åtkomstprincip på en behållare. Du kan använda åtkomstprincipen för att ange begränsningar för en service-SAS på behållaren eller dess blobbar.

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Se även

- [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SIGNATURER för delad åtkomst (SAS)](storage-sas-overview.md)
- [Definiera en lagrad åtkomstprincip](/rest/api/storageservices/define-stored-access-policy)

