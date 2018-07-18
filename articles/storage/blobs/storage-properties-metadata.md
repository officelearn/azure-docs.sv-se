---
title: Ange och hämta egenskaper och metadata i Azure Storage | Microsoft Docs
description: Store anpassade metadata för objekt i Azure Storage, och ange och hämta Systemegenskaper.
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
ms.date: 07/16/2018
ms.author: tamram
ms.openlocfilehash: d2c95139d42f42e43e2fa6e587d5b1b13afdf1e9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112451"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Ange och hämta egenskaper och metadata

Objekt i Azure Storage support Systemegenskaper och användardefinierade metadata, förutom de data de innehåller. Den här artikeln beskriver hantera Systemegenskaper och användardefinierade metadata med den [Azure Storage-klientbiblioteket för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Systemegenskaper**: Systemegenskaper finns på varje resurs för lagring. Vissa av dem kan läsas eller ange, medan andra är skrivskyddade. Under försättsbladen motsvarar vissa Systemegenskaper vissa standard HTTP-huvuden. Azure Storage-klientbibliotek underhålla dessa egenskaper åt dig.

* **Användardefinierade metadata**: användardefinierade metadata består av en eller flera namn / värde-par som du anger för en Azure Storage-resurs. Du kan använda metadata för att lagra ytterligare värden med en resurs. Metadatavärden efter behov endast, och påverkar inte hur resursen fungerar.

Hämtar värden för egenskapen och metadata för en resurs för lagring är en tvåstegsprocess. Innan du kan läsa dessa värden, måste du uttryckligen hämta dem genom att anropa den **FetchAttributes** eller **FetchAttributesAsync** metod. Undantaget är om du anropar den **Exists** eller **ExistsAsync** metod på en resurs. När du anropar någon av följande metoder, Azure Storage anropar rätt **FetchAttributes** metod under försättsbladen som en del av anropet till den **Exists** metod.

> [!IMPORTANT]
> Om du upptäcker att värdena för egenskapen eller metadata för en storage-resurs inte är ifyllda, kontrollerar du att koden anropar den **FetchAttributes** eller **FetchAttributesAsync** metod.
>
> Metadata namn/värde-par kan endast innehålla ASCII-tecken. Metadata namn/värde-par är giltig HTTP-huvuden och därför måste följa alla begränsningar som reglerar HTTP-huvuden. Du rekommenderas att du använder URL-kodning eller Base64-kodning för namn och värden som innehåller icke-ASCII-tecken.
>

## <a name="setting-and-retrieving-properties"></a>Ange och läsa egenskaper
Om du vill hämta egenskapsvärden anropa den **FetchAttributesAsync** metod på din blob eller behållare för att fylla i egenskaperna kan sedan läsa värdena.

Ange egenskaper för ett objekt genom att ange egenskapen värdet och sedan anropa den **SetProperties** metod.

Följande exempel skapar en behållare och skriver sedan några av dess egenskapsvärden till ett konsolfönster.

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
Du kan ange metadata som en eller flera namn / värde-par för en blob eller behållare resurs. Ange metadata genom att lägga till namn / värde-par till den **Metadata** samling på resurs kan sedan anropa den **SetMetadata** eller **SetMetadataAsync** metod för att spara värdena som den tjänsten.

> [!NOTE]
> Namnet på dina metadata uppfylla namnkonventionerna för C#-identifierare.
>
>

I följande kodexempel anger metadata för en behållare. Ett värde ställs in med samlingen **Lägg till** metod. Det andra värdet med implicit nyckel/värde-syntax. Båda är giltiga.

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

Hämta metadata genom att anropa den **FetchAttributes** eller **FetchAttributesAsync** metod på din blob eller behållare för att fylla i den **Metadata** samling, läs sedan de värden som visas i exemplet nedan.

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
* [Azure Storage-klientbiblioteket för .NET-referens](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Azure Storage-klientbiblioteket för .NET NuGet-paket](https://www.nuget.org/packages/WindowsAzure.Storage/)
