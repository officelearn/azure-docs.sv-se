---
title: Ange en kundklämmen på en begäran till Blob-lagring med .NET - Azure Storage
description: Lär dig hur du anger en kundad nyckel på en begäran till Blob-lagring med .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807008"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Ange en kundklämmen på en begäran till Blob-lagring med .NET

Klienter som gör begäranden mot Azure Blob-lagring har möjlighet att ange en krypteringsnyckel på en enskild begäran. Inklusive krypteringsnyckeln på begäran ger detaljerad kontroll över krypteringsinställningar för Blob-lagringsåtgärder. Nycklar som tillhandahålls av kunden (förhandsversion) kan lagras i Azure Key Vault eller i en annan nyckelbutik.

Den här artikeln visar hur du anger en kundad nyckel på en begäran med .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Mer information om hur du autentiserar med Azure Identity-klientbiblioteket från Azure Storage finns i avsnittet **Autentisera med Azure Identity-biblioteket** i [Auktorisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Exempel: Använd en kundklump för att ladda upp en blob

I följande exempel skapas en kundklump och den nyckeln används för att ladda upp en blob. Koden överför ett block och genomför sedan blockeringslistan för att skriva blobben till Azure Storage.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering för data i vila](../common/storage-service-encryption.md)
- [Auktorisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](../common/storage-auth-aad-msi.md)
