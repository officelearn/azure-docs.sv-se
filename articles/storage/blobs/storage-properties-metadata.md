---
title: Ange och hämta objektets egenskaper och metadata i Azure Storage | Microsoft Docs
description: Lagra anpassade metadata för objekt i Azure Storage, och ange och hämta Systemegenskaper.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: a3eb598b2dabd4986c72b8814926eb0944707050
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873032"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Ange och hämta egenskaper och metadata

Objekt i Azure Storage stödsystem egenskaper och användardefinierade metadata, förutom de data som de innehåller. Den här artikeln beskrivs hantera Systemegenskaper och användardefinierade metadata med den [Azure Storage-klientbibliotek för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Systemegenskaper**: Systemegenskaper finns på varje storage-resurs. Några av dem kan läsa eller ange, medan andra är skrivskyddad. Under försättsbladen motsvarar vissa Systemegenskaper vissa vanliga HTTP-huvuden. Azure storage-klientbiblioteket underhåller dessa åt dig.

* **Användardefinierade metadata**: användardefinierade metadata är metadata som du anger på en viss resurs i form av ett namn / värde-par. Du kan använda metadata för att lagra ytterligare värden med en lagringsresurs. Värdena ytterligare metadata egna endast för, och påverkar inte hur resursen ska fungera.

Hämtar värden för egenskapen och metadata för en lagringsresurs är en tvåstegsprocess. Innan du kan läsa dessa värden, du måste uttryckligen hämta dem genom att anropa den **FetchAttributesAsync** metod.

> [!IMPORTANT]
> Värden för egenskapen och metadata för en lagringsresurs fylls inte om du anropar en av de **FetchAttributesAsync** metoder.
>
> Du får en `400 Bad Request` om alla namn/värde-par innehåller icke-ASCII-tecken. Metadata namn/värde-par är giltig HTTP-huvuden och därför måste följa alla begränsningar för HTTP-huvuden. Du bör därför att du använder URL-kodning eller Base64-kodning för namn och värden som innehåller icke-ASCII-tecken.
>

## <a name="setting-and-retrieving-properties"></a>Ange och läsa egenskaper
För att hämta egenskapsvärden anropa den **FetchAttributesAsync** metod på blob eller behållare för att fylla i egenskaperna sedan läsa värdena.

Ange egenskaper för ett objekt genom att ange egenskapen värdet och sedan anropa den **SetProperties** metod.

Följande exempel skapar en behållare och sedan skriver några av dess egenskapsvärden till konsolfönstret.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Ange och läsa metadata
Du kan ange metadata som en eller flera namn-värdepar för en blob eller behållaren resurs. Lägg till namn / värde-par för att ange metadata, den **Metadata** samlingen på resursen, anropar den **SetMetadata** metod för att spara värdena till tjänsten.

> [!NOTE]
> Namnet på dina metadata måste följa namnkonventionerna för C#-identifierare.
>
>

Följande exempel anger metadata för en behållare. Ett värde ställs in med mängdens **Lägg till** metod. Det andra värdet med implicit nyckel/värde-syntax. Båda är giltiga.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

För att hämta metadata anropa den **FetchAttributes** metod på blob eller behållare för att fylla i **Metadata** samling, Läs värden som visas i exemplet nedan.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
{
    // Fetch container attributes in order to populate the container's properties and metadata.
    await container.FetchAttributesAsync();

    // Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>Nästa steg
* [Azure Storage-klientbibliotek för .NET-referens](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Azure Storage-klientbibliotek för .NET-NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.Storage/)
