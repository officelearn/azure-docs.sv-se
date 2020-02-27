---
title: Hantera egenskaper och metadata för en blob med .NET-Azure Storage
description: Lär dig hur du ställer in och hämtar system egenskaper och lagrar anpassade metadata på blobbar i ditt Azure Storage konto med hjälp av .NET-klient biblioteket.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 395210c582ba7f5e8170a96a46e2816336e33b2d
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624038"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Hantera BLOB-egenskaper och metadata med .NET

Förutom de data som de innehåller, stöder blobbar system egenskaper och användardefinierade metadata. Den här artikeln visar hur du hanterar system egenskaper och användardefinierade metadata med [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>Om egenskaper och metadata

- **System egenskaper**: system egenskaper finns på varje Blob Storage-resurs. En del av dem kan läsas eller anges, medan andra är skrivskyddade. Under försättsblad motsvarar vissa system egenskaper vissa standard-HTTP-huvuden. Azure Storage klient bibliotek för .NET underhåller dessa egenskaper åt dig.

- **Användardefinierade metadata**: användardefinierade metadata består av ett eller flera namn-värdepar som du anger för en Blob Storage-resurs. Du kan använda metadata för att lagra ytterligare värden med resursen. Metadata-värden är enbart för egna behov och påverkar inte hur resursen beter sig.

Hämtning av metadata och egenskaps värden för en Blob Storage-resurs är en två stegs process. Innan du kan läsa dessa värden måste du explicit hämta dem genom att anropa metoden `FetchAttributes` eller `FetchAttributesAsync`. Undantaget till den här regeln är att `Exists` och `ExistsAsync` metoder anropar rätt `FetchAttributes`-metod under försättsblad. När du anropar någon av dessa metoder behöver du inte också anropa `FetchAttributes`.

> [!IMPORTANT]
> Om du upptäcker att egenskaps-eller metadata-värden för en lagrings resurs inte har fyllts i kontrollerar du att koden anropar `FetchAttributes`-eller `FetchAttributesAsync`-metoden.

## <a name="set-and-retrieve-properties"></a>Ange och hämta egenskaper

I följande kod exempel anges `ContentType` och `ContentLanguage` system egenskaper på en blob.

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

Om du vill hämta BLOB-egenskaper anropar du `FetchAttributes`-eller `FetchAttributesAsync`-metoden på blobben för att fylla i `Properties`-egenskapen. I följande kod exempel hämtas system egenskaper för en blob och några av värdena visas:

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

Du kan ange metadata som ett eller flera namn-värdepar på en BLOB-eller container resurs. Om du vill ange metadata lägger du till namn-värdepar i `Metadata` samlingen på resursen. Anropa sedan någon av följande metoder för att skriva värdena:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Metadata-/värdepar är giltiga HTTP-huvuden och bör följa alla begränsningar som gäller för HTTP-huvuden. Metadata-namn måste vara giltiga HTTP-huvudnamn C# och giltiga identifierare, får bara innehålla ASCII-tecken och bör behandlas som Skift läges okänsligt. [Base64-koda](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) eller [URL-koda metadata-](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) värden som innehåller icke-ASCII-tecken.

Namnet på dina metadata måste följa namngivnings konventionerna för C# identifierare. Metadata-namn upprätthåller när de skapades, men är Skift läges känsliga när de har angetts eller lästs. Om två eller flera metadata-huvuden med samma namn skickas för en resurs returnerar Azure Blob Storage HTTP-felkoden 400 (felaktig begäran).

I följande kod exempel anges metadata för en blob. Ett värde anges med samlingens `Add` metod. Det andra värdet anges med implicit nyckel/värde-syntax.

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

Om du vill hämta metadata anropar du `FetchAttributes`-eller `FetchAttributesAsync`-metoden på bloben eller behållaren för att fylla i `Metadata` samlingen och läser sedan värdena, som du ser i exemplet nedan.

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

- [Ange BLOB Properties-åtgärd](/rest/api/storageservices/set-blob-properties)
- [Hämta BLOB Properties-åtgärd](/rest/api/storageservices/get-blob-properties)
- [Ange åtgärd för BLOB-metadata](/rest/api/storageservices/set-blob-metadata)
- [Hämta BLOB metadata-åtgärd](/rest/api/storageservices/get-blob-metadata)
