---
title: Skapa en användar Delegerings-SAS för en behållare eller BLOB med .NET (för hands version) – Azure Storage
description: Lär dig hur du skapar en användar Delegerings-SAS med Azure Active Directory autentiseringsuppgifter i Azure Storage med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 59de768e75a88d7cfa5b68fa306d0e83f1aa0ba3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671332"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Skapa en användar Delegerings-SAS för en behållare eller BLOB med .NET (för hands version)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD)-autentiseringsuppgifter för att skapa en användar Delegerings-sa för en behållare eller BLOB med [Azure Storage klient biblioteket för .net](https://www.nuget.org/packages/Azure.Storage.Blobs).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>Installera för hands versions paketen

I exemplen i den här artikeln används den senaste för hands versionen av Azure Storage klient biblioteket för Blob Storage. För att installera för hands versions paketet kör du följande kommando från NuGet Package Manager-konsolen:

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

I exemplen i den här artikeln används även den senaste för hands versionen av [klient biblioteket för Azure Identity för .net](https://www.nuget.org/packages/Azure.Identity/) för att autentisera med Azure AD-autentiseringsuppgifter. Klient biblioteket för Azure Identity autentiserar ett säkerhets objekt. Autentiserade säkerhets objekt kan sedan skapa användar Delegerings-SAS. Mer information om klient biblioteket för Azure Identity finns i [klient biblioteket för Azure Identity för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om du vill autentisera med Azure AD-autentiseringsuppgifter via klient biblioteket för Azure Identity, använder du antingen ett tjänstens huvud namn eller en hanterad identitet som säkerhets objekt, beroende på var koden körs. Om din kod körs i en utvecklings miljö använder du ett huvud namn för tjänsten i test syfte. Om din kod körs i Azure använder du en hanterad identitet. Den här artikeln förutsätter att du kör kod från utvecklings miljön och visar hur du använder ett huvud namn för tjänsten för att skapa användar Delegerings-SAS.

Om du vill skapa ett huvud namn för tjänsten med Azure CLI och tilldela en RBAC-roll, anropar du kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Ange en Azure Storage data åtkomst roll som ska tilldelas det nya huvud namnet för tjänsten. Rollen måste omfatta åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Mer information om de inbyggda roller som finns för Azure Storage finns [inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

Ange dessutom omfånget för roll tilldelningen. Tjänstens huvud namn skapar användar Delegerings nyckeln, som är en åtgärd som utförs på lagrings kontots nivå, så att roll tilldelningen ska begränsas på lagrings kontots, resurs gruppens eller prenumerationens nivå. Mer information om RBAC-behörigheter för att skapa en användar Delegerings-SAS finns i avsnittet **tilldela behörigheter med RBAC** i [skapa en användar delegering SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

Om du inte har tillräcklig behörighet för att tilldela en roll till tjänstens huvud namn kan du behöva be kontots ägare eller administratör att utföra roll tilldelningen.

I följande exempel används Azure CLI för att skapa ett nytt huvud namn för tjänsten och tilldela rollen **Storage BLOB data Reader** till den med konto omfånget

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Kommandot `az ad sp create-for-rbac` returnerar en lista över egenskaper för tjänstens huvud namn i JSON-format. Kopiera värdena så att du kan använda dem för att skapa de miljövariabler som krävs i nästa steg.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Det kan ta några minuter att sprida RBAC-roll tilldelningar.

## <a name="set-environment-variables"></a>Ange miljövariabler

Klient biblioteket för Azure Identity läser värden från tre miljövariabler vid körning för att autentisera tjänstens huvud namn. I följande tabell beskrivs det värde som ska anges för varje miljö variabel.

|Miljövariabel|Value
|-|-
|`AZURE_CLIENT_ID`|App-ID för tjänstens huvud namn
|`AZURE_TENANT_ID`|Tjänstens huvud namn för Azure AD-klient
|`AZURE_CLIENT_SECRET`|Lösen ordet som genereras för tjänstens huvud namn

> [!IMPORTANT]
> När du har ställt in miljövariablerna stänger du och öppnar konsol fönstret igen. Om du använder Visual Studio eller en annan utvecklings miljö kan du behöva starta om utvecklings miljön för att den ska kunna registrera nya miljövariabler.

## <a name="add-using-directives"></a>Lägga till med hjälp av direktiv

Lägg till följande `using`-direktiv i koden för att använda för hands versionerna av Azure Identity och Azure Storage klient bibliotek.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="authenticate-the-service-principal"></a>Autentisera tjänstens huvud namn

Om du vill autentisera tjänstens huvud namn skapar du en instans av klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . Konstruktorn `DefaultAzureCredential` läser de miljövariabler som du skapade tidigare.

Följande kodfragment visar hur du hämtar autentiserade autentiseringsuppgifter och använder den för att skapa en tjänst klient för Blob Storage

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

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
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Skapa SAS-token

Följande kodfragment visar hur du skapar en ny [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) och anger parametrarna för användar Delegerings-SAS. Kodfragmentet anropar sedan [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) för att hämta SAS-token-strängen. Slutligen skapar koden hela URI: n, inklusive resurs adressen och SAS-token.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

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
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

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
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Exempel: Läsa en blob med en användar Delegerings-SAS

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
    catch (StorageRequestFailedException e)
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
