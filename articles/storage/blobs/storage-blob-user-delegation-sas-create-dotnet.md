---
title: Skapa en användar Delegerings-SAS för en behållare eller BLOB med .NET (för hands version) – Azure Storage
description: Lär dig hur du skapar en användar Delegerings-SAS (för hands version) med Azure Active Directory autentiseringsuppgifter i Azure Storage med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 32bcb12f39d65d63af1c6595c0d57c695ce0533f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792210"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Skapa en användar Delegerings-SAS för en behållare eller BLOB med .NET (för hands version)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD)-autentiseringsuppgifter för att skapa en användar Delegerings-SAS (för hands version) för en behållare eller BLOB med Azure Storage klient biblioteket för .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library"></a>Autentisera med Azure Identity Library

Klient biblioteket för Azure Identity för .NET autentiserar ett säkerhets objekt. När din kod körs i Azure är säkerhets objekt en hanterad identitet för Azure-resurser.

När din kod körs i utvecklings miljön kan autentiseringen hanteras automatiskt, eller så kan det krävas en webb läsar inloggning, beroende på vilka verktyg du använder. Microsoft Visual Studio stöder enkel inloggning (SSO), så att det aktiva Azure AD-användarkontot används automatiskt för autentisering. Mer information om SSO finns i [enkel inloggning till program](../../active-directory/manage-apps/what-is-single-sign-on.md).

Andra utvecklingsverktyg kan bli ombedd att logga in via en webbläsare. Du kan också använda ett huvud namn för tjänsten för att autentisera från utvecklings miljön. Mer information finns i [skapa identitet för Azure-appen i portalen](../../active-directory/develop/howto-create-service-principal-portal.md).

Efter autentiseringen får klient biblioteket för Azure Identity ett token-autentiseringsuppgifter. Den här token-autentiseringsuppgiften kapslas sedan i det tjänst klient objekt som du skapar för att utföra åtgärder mot Azure Storage. Biblioteket hanterar detta för sömlöst genom att hämta rätt autentiseringsuppgifter för token.

Mer information om klient biblioteket för Azure Identity finns i [klient biblioteket för Azure Identity för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Tilldela RBAC-roller för åtkomst till data

När ett Azure AD-säkerhetsobjekt försöker få åtkomst till BLOB-data måste säkerhets objektets behörigheter ha behörighet till resursen. Om säkerhetsobjektet är en hanterad identitet i Azure eller ett Azure AD-användarkonto som kör kod i utvecklings miljön, måste säkerhets objekt tilldelas en RBAC-roll som ger åtkomst till BLOB-data i Azure Storage. Information om hur du tilldelar behörigheter via RBAC finns i avsnittet **tilldela RBAC-roller för åtkomst rättigheter** i [auktorisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-packages"></a>Installera paketen

I exemplen i den här artikeln används den senaste versionen av [Azure Storage klient biblioteket för Blob Storage](https://www.nuget.org/packages/Azure.Storage.Blobs). Installera paketet genom att köra följande kommando från NuGet Package Manager-konsolen:

```powershell
Install-Package Azure.Storage.Blobs
```

I exemplen i den här artikeln används även den senaste versionen av [klient biblioteket för Azure Identity för .net](https://www.nuget.org/packages/Azure.Identity/) för att autentisera med Azure AD-autentiseringsuppgifter. Installera paketet genom att köra följande kommando från NuGet Package Manager-konsolen:

```powershell
Install-Package Azure.Identity
```

## <a name="add-using-directives"></a>Lägga till med hjälp av direktiv

Lägg till följande `using`-direktiv i koden för att använda klient biblioteken för Azure-identitet och Azure Storage.

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

## <a name="get-an-authenticated-token-credential"></a>Hämta autentiseringsuppgifter för autentiserad token

Skapa en instans av klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) om du vill hämta en token-autentiseringsuppgifter som din kod kan använda för att auktorisera begär anden till Azure Storage.

Följande kodfragment visar hur du hämtar autentiseringsuppgifter för autentiserade token och använder den för att skapa en tjänst klient för Blob Storage:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Hämta användar Delegerings nyckeln

Varje SAS signeras med en nyckel. Om du vill skapa en användar Delegerings-SAS måste du först begära en användar Delegerings nyckel som sedan används för att signera SAS. Användar Delegerings nyckeln motsvarar den konto nyckel som används för att signera en tjänst-SAS eller en konto säkerhets Association, förutom att den förlitar sig på dina autentiseringsuppgifter för Azure AD. När en klient begär en användar Delegerings nyckel med en OAuth 2,0-token returnerar Azure Storage användar Delegerings nyckeln för användarens räkning.

När du har en användar Delegerings nyckel kan du använda den nyckeln för att skapa ett valfritt antal signaturer för delad åtkomst för användar delegering, under nyckelns livstid. Användar Delegerings nyckeln är oberoende av OAuth 2,0-token som används för att hämta den, så token behöver inte förnyas så länge nyckeln fortfarande är giltig. Du kan ange att nyckeln är giltig för en period på upp till sju dagar.

Använd någon av följande metoder för att begära användar Delegerings nyckeln:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Följande kodfragment hämtar användar Delegerings nyckeln och skriver ut dess egenskaper:

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

Följande kodfragment visar hur du skapar en ny [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) och anger parametrarna för användar Delegerings-SAS. Kodfragmentet anropar sedan [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) för att hämta SAS-token-strängen. Slutligen skapar koden hela URI: n, inklusive resurs adressen och SAS-token.

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

## <a name="example-get-a-user-delegation-sas"></a>Exempel: Hämta en användar Delegerings-SAS

Följande exempel metod visar den fullständiga koden för att autentisera säkerhets objekt och skapa användar Delegerings-SAS:

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

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Exempel: läsa en blob med en användar Delegerings-SAS

I följande exempel testas de användar Delegerings-SAS som skapades i föregående exempel från ett simulerat klient program. Om SAS är giltig kan klient programmet läsa innehållet i blobben. Om SAS är ogiltig, till exempel om den har upphört att gälla, returnerar Azure Storage felkod 403 (förbjuden).

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

## <a name="see-also"></a>Se också

- [Hämta åtgärd för användar Delegerings nyckel](/rest/api/storageservices/get-user-delegation-key)
- [Skapa en användar Delegerings-SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
