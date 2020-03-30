---
title: Använda .NET för att hantera egenskaper och metadata för en blob-behållare
titleSuffix: Azure Storage
description: Lär dig hur du ställer in och hämtar systemegenskaper och lagrar anpassade metadata på blob-behållare i ditt Azure Storage-konto med hjälp av .NET-klientbiblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135929"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Hantera behållaregenskaper och metadata med .NET

Blob-behållare stöder systemegenskaper och användardefinierade metadata, utöver de data de innehåller. Den här artikeln visar hur du hanterar systemegenskaper och användardefinierade metadata med [Azure Storage-klientbiblioteket för .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-properties-and-metadata"></a>Om egenskaper och metadata

- **Systemegenskaper**: Systemegenskaper finns på varje Blob-lagringsresurs. Vissa av dem kan läsas eller ställas in, medan andra är skrivskyddade. Under skalskydden motsvarar vissa systemegenskaper vissa standard-HTTP-huvuden. Azure Storage-klientbiblioteket för .NET underhåller dessa egenskaper åt dig.

- **Användardefinierade metadata**: Användardefinierade metadata består av ett eller flera namnvärdespar som du anger för en Blob-lagringsresurs. Du kan använda metadata för att lagra ytterligare värden med resursen. Metadatavärden är endast för dina egna syften och påverkar inte hur resursen beter sig.

Att hämta egenskaps- och metadatavärden för en Blob-lagringsresurs är en tvåstegsprocess. Innan du kan läsa dessa värden måste du uttryckligen hämta dem genom att anropa metoden **FetchAttributes** eller **FetchAttributesAsync.** Undantaget från den här regeln är att metoderna **Exists** och **ExistsAsync** anropar lämplig **Metoden FetchAttributes** under omfattar. När du anropar någon av dessa metoder behöver du inte heller anropa **FetchAttributes**.

> [!IMPORTANT]
> Om du upptäcker att egenskaps- eller metadatavärden för en lagringsresurs inte har fyllts i kontrollerar du att koden anropar metoden **FetchAttributes** eller **FetchAttributesAsync.**

Metadatanamn/värdepar är giltiga HTTP-huvuden och bör därför följa alla begränsningar som styr HTTP-huvuden. Metadatanamn måste vara giltiga HTTP-huvudnamn och giltiga C#-identifierare, får endast innehålla ASCII-tecken och bör behandlas som skiftlägesokänsliga. Metadatavärden som innehåller icke-ASCII-tecken ska vara Base64-kodade eller URL-kodade.

## <a name="retrieve-container-properties"></a>Hämta behållaregenskaper

Om du vill hämta behållaregenskaper anropar du någon av följande metoder:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

I följande kodexempel hämtas en behållares systemegenskaper och vissa egenskapsvärden skrivs till ett konsolfönster:

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

Du kan ange metadata som ett eller flera namnvärdespar på en blob- eller behållarresurs. Om du vill ange metadata lägger du till namnvärdespar i **metadatasamlingen** på resursen och anropar sedan någon av följande metoder för att skriva värdena:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Namnet på dina metadata måste överensstämma med namngivningskonventionerna för C#-identifierare. Metadatanamn bevarar det ärende som de skapades med, men är skiftlägesokänsliga när de ställs in eller läses. Om två eller flera metadatarubriker med samma namn skickas för en resurs returnerar Blob-lagring HTTP-felkod 400 (Felaktig begäran).

I följande kodexempel anges metadata på en behållare. Ett värde anges med hjälp **Add** av samlingens Add-metod. Det andra värdet anges med implicit nyckel/värdesyntax. Båda är giltiga.

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

Om du vill hämta metadata anropar du metoden **FetchAttributes** eller **FetchAttributesAsync** på din blob eller behållare för att fylla i **metadatasamlingen** och läser sedan värdena, som visas i exemplet nedan.

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

## <a name="see-also"></a>Se även

- [Åtgärden Hämta behållaregenskaper](/rest/api/storageservices/get-container-properties)
- [Ange åtgärden Behållarindata](/rest/api/storageservices/set-container-metadata)
- [Hämta åtgärden Behålla metadata](/rest/api/storageservices/set-container-metadata)
