---
title: Använda .NET för att skapa en SAS för användardelegering för en behållare eller blob
titleSuffix: Azure Storage
description: Lär dig hur du skapar en SAS för användardelegering med Azure Active Directory-autentiseringsuppgifter med hjälp av .NET-klientbiblioteket för Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371844"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Skapa en SAS för användardelegering för en behållare eller blob med .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD) autentiseringsuppgifter för att skapa en användaredelegering SAS för en behållare eller blob med Azure Storage-klientbiblioteket för .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>Tilldela RBAC-roller för åtkomst till data

När ett Azure AD-säkerhetsobjekt försöker komma åt blob-data måste säkerhetsobjektet ha behörighet till resursen. Oavsett om säkerhetsobjektet är en hanterad identitet i Azure eller ett Azure AD-användarkonto som kör kod i utvecklingsmiljön, måste säkerhetsobjektet tilldelas en RBAC-roll som ger åtkomst till blob-data i Azure Storage. Information om hur du tilldelar behörigheter via RBAC finns i avsnittet **Tilldela RBAC-roller för åtkomsträttigheter** i [Auktorisera åtkomst till Azure-blobbar och köer med Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Mer information om hur du autentiserar med Azure Identity-klientbiblioteket från Azure Storage finns i avsnittet **Autentisera med Azure Identity-biblioteket** i [Auktorisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="add-using-directives"></a>Lägga till med hjälp av direktiv

Lägg till `using` följande direktiv i koden för att använda Azure Identity- och Azure Storage-klientbiblioteken.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Hämta en autentrad tokenautentiseringsåtkomst

Skapa en instans av klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) för att få en tokenautentiseringsinformation som koden kan använda för att auktorisera begäranden till Azure Storage.

Följande kodavsnitt visar hur du hämtar den autentiserade tokenautentiseringsuppgifterna och använder det för att skapa en tjänstklient för Blob-lagring:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Hämta nyckeln för användardelegering

Varje SAS är signerad med en nyckel. Om du vill skapa en SAS-användardelegering måste du först begära en användardelegeringsnyckel som sedan används för att signera SAS. Användardelegeringsnyckeln är jämförbar med den kontonyckel som används för att signera en tjänst SAS eller ett konto SAS, förutom att den är beroende av dina Azure AD-autentiseringsuppgifter. När en klient begär en användardelegeringsnyckel med hjälp av en OAuth 2.0-token returnerar Azure Storage användardelegeringsnyckeln för användarens räkning.

När du har användardelegeringsnyckeln kan du använda den nyckeln för att skapa valfritt antal signaturer för delad åtkomst för användare under nyckelns livstid. Användardelegeringsnyckeln är oberoende av den OAuth 2.0-token som används för att hämta den, så token behöver inte förnyas så länge nyckeln fortfarande är giltig. Du kan ange att nyckeln ska vara giltig i upp till 7 dagar.

Använd någon av följande metoder för att begära användardelegeringsnyckeln:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Följande kodavsnitt hämtar användardelegeringsnyckeln och skriver ut dess egenskaper:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Skapa SAS-token

Följande kodavsnitt visar skapa en ny [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) och ange parametrar för användarendelegationen SAS. Kodavsnittet anropar sedan [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) för att hämta SAS-tokensträngen. Slutligen skapar koden hela URI, inklusive resursadressen och SAS-token.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Exempel: Hämta en SAS-användaredelegering

Följande exempelmetod visar den fullständiga koden för att autentisera säkerhetsobjektet och skapa SAS för användardelegation:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Exempel: Läs en blob med en sas-för användardelegering

I följande exempel testas den SAS som skapats i föregående exempel från ett simulerat klientprogram. Om SAS är giltigt kan klientprogrammet läsa innehållet i blobben. Om SAS är ogiltigt, till exempel om den har upphört att gälla, returnerar Azure Storage felkod 403 (Förbjudet).

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid,
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Se även

- [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SIGNATURER för delad åtkomst (SAS)](../common/storage-sas-overview.md)
- [Hämta åtgärden Nyckel för användardelegering](/rest/api/storageservices/get-user-delegation-key)
- [Skapa en SAS -för användardelegation (REST API)](/rest/api/storageservices/create-user-delegation-sas)
