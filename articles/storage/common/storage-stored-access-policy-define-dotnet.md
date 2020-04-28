---
title: Definiera en lagrad åtkomst princip med .NET-Azure Storage
description: Lär dig hur du definierar en lagrad åtkomst princip med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "68990746"
---
# <a name="define-a-stored-access-policy-with-net"></a>Definiera en lagrad åtkomst princip med .NET

En lagrad åtkomst princip ger ytterligare kontroll nivå över signaturer för delad åtkomst på tjänst nivå (SAS) på Server sidan. Att definiera en lagrad åtkomst princip används för att gruppera signaturer för delad åtkomst och för att ge ytterligare begränsningar för signaturer för delad åtkomst som är kopplade till principen. Du kan använda en lagrad åtkomst princip för att ändra start tid, förfallo tid eller behörigheter för en SAS eller återkalla den när den har utfärdats.
  
 Följande lagrings resurser har stöd för lagrade åtkomst principer:  
  
- Blob-containrar  
- Filresurser  
- Köer  
- Tabeller  
  
> [!NOTE]
> En lagrad åtkomst princip på en behållare kan associeras med en signatur för delad åtkomst som beviljar behörigheter till själva behållaren eller till de blobbar som den innehåller. På samma sätt kan en lagrad åtkomst princip på en fil resurs associeras med en signatur för delad åtkomst som beviljar behörighet till själva resursen eller filer som den innehåller.  
>
> Lagrade åtkomst principer stöds endast för en tjänst-SAS. Lagrade åtkomst principer stöds inte för konto-SAS eller användar Delegerings-SAS.  

## <a name="create-a-stored-access-policy"></a>Skapa en lagrad åtkomst princip

Följande kod skapar en lagrad åtkomst princip på en behållare. Du kan använda åtkomst principen för att ange begränsningar för en tjänst-SAS på behållaren eller dess blobbar.

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

- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](storage-sas-overview.md)
- [Definiera en lagrad åtkomstprincip](/rest/api/storageservices/define-stored-access-policy)

