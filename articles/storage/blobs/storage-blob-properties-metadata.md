---
title: Hantera egenskaper och metadata för en blob med .NET - Azure Storage
description: Lär dig hur du anger och hämtar systemegenskaper och lagrar anpassade metadata på blobbar i ditt Azure Storage-konto med hjälp av .NET-klientbiblioteket.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137670"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Hantera blob-egenskaper och metadata med .NET

Förutom de data de innehåller stöder blobbar systemegenskaper och användardefinierade metadata. Den här artikeln visar hur du hanterar systemegenskaper och användardefinierade metadata med [Azure Storage-klientbiblioteket för .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Om egenskaper och metadata

- **Systemegenskaper**: Systemegenskaper finns på varje Blob-lagringsresurs. Vissa av dem kan läsas eller ställas in, medan andra är skrivskyddade. Under skalskydden motsvarar vissa systemegenskaper vissa standard-HTTP-huvuden. Azure Storage-klientbiblioteket för .NET underhåller dessa egenskaper åt dig.

- **Användardefinierade metadata**: Användardefinierade metadata består av ett eller flera namnvärdespar som du anger för en Blob-lagringsresurs. Du kan använda metadata för att lagra ytterligare värden med resursen. Metadatavärden är endast för dina egna syften och påverkar inte hur resursen beter sig.

Att hämta metadata och egenskapsvärden för en Blob-lagringsresurs är en tvåstegsprocess. Innan du kan läsa dessa värden måste du `FetchAttributes` `FetchAttributesAsync` uttryckligen hämta dem genom att anropa metoden eller. Undantaget från denna regel `Exists` är `ExistsAsync` att och `FetchAttributes` metoderna anropar lämplig metod under omslagen. När du ringer en av dessa metoder behöver `FetchAttributes`du inte också ringa.

> [!IMPORTANT]
> Om du upptäcker att egenskaps- eller metadatavärden för en lagringsresurs inte har fyllts i kontrollerar du att koden anropar `FetchAttributes` metoden eller. `FetchAttributesAsync`

## <a name="set-and-retrieve-properties"></a>Ange och hämta egenskaper

I följande kodexempel anges `ContentType` egenskaperna och `ContentLanguage` system på en blob.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Om du vill hämta `FetchAttributes` blob-egenskaper anropar du metoden eller `FetchAttributesAsync` på bloben för att fylla i egenskapen. `Properties` I följande kodexempel hämtas en blobs systemegenskaper och några av värdena visas:

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>Ange och hämta metadata

Du kan ange metadata som ett eller flera namnvärdespar på en blob- eller behållarresurs. Om du vill ange metadata lägger `Metadata` du till namn-värde-par i samlingen på resursen. Anropa sedan någon av följande metoder för att skriva värdena:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Metadatanamn/värdepar är giltiga HTTP-huvuden och bör följa alla begränsningar som styr HTTP-huvuden. Metadatanamn måste vara giltiga HTTP-huvudnamn och giltiga C#-identifierare, får endast innehålla ASCII-tecken och bör behandlas som skiftlägesokänsliga. [Base64-koda](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) eller [URL-koda](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) metadatavärden som innehåller icke-ASCII-tecken.

Namnet på dina metadata måste överensstämma med namngivningskonventionerna för C#-identifierare. Metadatanamn behåller det fall som användes när de skapades, men är skiftlägesokänsliga när de anges eller läses. Om två eller flera metadatahuvuden med samma namn skickas för en resurs returnerar Azure Blob-lagring HTTP-felkod 400 (Felaktig begäran).

I följande kodexempel anges metadata på en blob. Ett värde anges med hjälp `Add` av samlingens metod. Det andra värdet anges med implicit nyckel/värdesyntax.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Om du vill `FetchAttributes` hämta `FetchAttributesAsync` metadata anropar du metoden eller `Metadata` på din blob eller behållare för att fylla i samlingen och läser sedan värdena, som visas i exemplet nedan.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Se även

- [Ange åtgärd för Blob-egenskaper](/rest/api/storageservices/set-blob-properties)
- [Åtgärden Hämta Blob-egenskaper](/rest/api/storageservices/get-blob-properties)
- [Ange åtgärd för Blobmetadata](/rest/api/storageservices/set-blob-metadata)
- [Hämta åtgärd för Blobmetadata](/rest/api/storageservices/get-blob-metadata)
