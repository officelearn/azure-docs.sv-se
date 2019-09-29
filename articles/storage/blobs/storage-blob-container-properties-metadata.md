---
title: Hantera egenskaper och metadata för en BLOB-behållare med .NET-Azure Storage
description: Lär dig hur du ställer in och hämtar system egenskaper och lagrar anpassade metadata i BLOB-behållare i ditt Azure Storage konto med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: tamram
ms.openlocfilehash: d63c78fedb8dbd48655d36fecc3544fd512072e3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673380"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Hantera behållar egenskaper och metadata med .NET

BLOB-behållare stöder system egenskaper och användardefinierade metadata, förutom de data som de innehåller. Den här artikeln visar hur du hanterar system egenskaper och användardefinierade metadata med [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>Om egenskaper och metadata

- **System egenskaper**: Det finns system egenskaper på varje Blob Storage-resurs. En del av dem kan läsas eller anges, medan andra är skrivskyddade. Under försättsblad motsvarar vissa system egenskaper vissa standard-HTTP-huvuden. Azure Storage klient bibliotek för .NET underhåller dessa egenskaper åt dig.

- **Användardefinierade metadata**: Användardefinierade metadata består av ett eller flera namn-värdepar som du anger för en Blob Storage-resurs. Du kan använda metadata för att lagra ytterligare värden med resursen. Metadata-värden är enbart för egna behov och påverkar inte hur resursen beter sig.

Att hämta egenskaps-och metadata-värden för en Blob Storage-resurs är en två stegs process. Innan du kan läsa dessa värden måste du explicit hämta dem genom att anropa metoden **FetchAttributes** eller **FetchAttributesAsync** . Undantaget till den här regeln är att metoderna **exists** och **ExistsAsync** anropar lämplig **FetchAttributes** -metod under försättsblad. När du anropar någon av dessa metoder behöver du inte också anropa **FetchAttributes**.

> [!IMPORTANT]
> Om du upptäcker att egenskaps-eller metadata-värden för en lagrings resurs inte har fyllts i kontrollerar du att koden anropar metoden **FetchAttributes** eller **FetchAttributesAsync** .

Metadata-namn/värde-par är giltiga HTTP-huvuden och bör därför följa alla begränsningar som gäller för HTTP-huvuden. Metadata-namn måste vara giltiga HTTP-huvudnamn C# och giltiga identifierare, får bara innehålla ASCII-tecken och bör behandlas som Skift läges okänsligt. Metadata-värden som innehåller icke-ASCII-tecken ska vara base64-kodade eller URL-kodade.

## <a name="retrieve-container-properties"></a>Hämta behållar egenskaper

Anropa någon av följande metoder för att hämta behållar egenskaper:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Följande kod exempel hämtar en behållares system egenskaper och skriver några egenskaps värden till ett konsol fönster:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

Du kan ange metadata som ett eller flera namn-värdepar på en BLOB-eller container resurs. Om du vill ange metadata lägger du till namn-värdepar i **metadata** -samlingen på resursen och anropar sedan någon av följande metoder för att skriva värdena:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Namnet på dina metadata måste följa namngivnings konventionerna för C# identifierare. Metadata-namn bevarar det Skift läge som de skapades i, men är Skift läges okänsliga när de har angetts eller lästs. Om två eller flera metadata-huvuden med samma namn skickas för en resurs returnerar Blob Storage HTTP-felkoden 400 (felaktig begäran).

I följande kod exempel anges metadata i en behållare. Ett värde anges med samlingens **Add** -metod. Det andra värdet anges med implicit nyckel/värde-syntax. Båda är giltiga.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Om du vill hämta metadata anropar du metoden **FetchAttributes** eller **FetchAttributesAsync** på blobben eller behållaren för att fylla i **metadata** -samlingen och läser sedan värdena, som visas i exemplet nedan.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
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

## <a name="see-also"></a>Se också

- [Hämta container egenskaper-åtgärd](/rest/api/storageservices/get-container-properties)
- [Ange metadata åtgärd för container](/rest/api/storageservices/set-container-metadata)
- [Hämta metadata åtgärd för behållare](/rest/api/storageservices/set-container-metadata)
