---
title: Skapa en tjänst-SAS för en behållare eller BLOB med .NET-Azure Storage
description: Lär dig hur du skapar en signatur för delad åtkomst för en behållare (SAS) för en behållare eller BLOB med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/09/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: ca419165d4dea2a04f51e7ab6f60d6e23f418933
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035626"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>Skapa en tjänst-SAS för en behållare eller BLOB med .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder lagrings konto nyckeln för att skapa en tjänst-SAS för en behållare eller BLOB med [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage/client).

## <a name="create-a-service-sas-for-a-blob-container"></a>Skapa en tjänst-SAS för en BLOB-behållare

Om du vill skapa en tjänst-SAS för en behållare anropar du metoden [CloudBlobContainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) .

I följande kod exempel skapas en SAS på en behållare. Om namnet på en befintlig lagrad åtkomst princip anges, är den principen associerad med SAS. Om ingen lagrad åtkomst princip anges skapar koden en ad hoc SAS på behållaren.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

## <a name="create-a-service-sas-for-a-blob"></a>Skapa en tjänst-SAS för en BLOB

Om du vill skapa en tjänst-SAS för en BLOB anropar du metoden [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) .

I följande kod exempel skapas en SAS på en blob. Om namnet på en befintlig lagrad åtkomst princip anges, är den principen associerad med SAS. Om ingen lagrad åtkomst princip anges skapar koden en ad hoc SAS på blobben.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Nästa steg

- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md)
- [Skapa en tjänst-SAS](/rest/api/storageservices/create-service-sas)
