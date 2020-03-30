---
title: Lista blobbar med .NET - Azure Storage
description: Lär dig hur du listar blobbar i en behållare i ditt Azure Storage-konto med hjälp av .NET-klientbiblioteket. Kodexempel visar hur du listar blobbar i en platt lista eller hur blobbar ska listas hierarkiskt, som om de var ordnade i kataloger eller mappar.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137928"
---
# <a name="list-blobs-with-net"></a>Lista blobbar med .NET

När du listar blobbar från din kod kan du ange ett antal alternativ för att hantera hur resultaten returneras från Azure Storage. Du kan ange hur många resultat som ska returneras i varje uppsättning resultat och sedan hämta efterföljande uppsättningar. Du kan ange ett prefix för att returnera blobbar vars namn börjar med det tecknet eller strängen. Och du kan lista blobbar i en platt liststruktur eller hierarkiskt. En hierarkisk lista returnerar blobbar som om de var ordnade i mappar. 

Den här artikeln visar hur du listar blobbar med hjälp av [Azure Storage-klientbiblioteket för .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-blob-listing-options"></a>Förstå alternativ för blob-listning

Om du vill visa blobbar i ett lagringskonto anropar du någon av följande metoder:

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmenterad](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Om du vill visa blobbar i en behållare anropar du någon av följande metoder:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Överbelastningarna för dessa metoder ger ytterligare alternativ för att hantera hur blobbar returneras av listningsåtgärden. Dessa alternativ beskrivs i följande avsnitt.

### <a name="manage-how-many-results-are-returned"></a>Hantera hur många resultat som returneras

Som standard returnerar en listning upp till 5000 resultat åt gången. Om du vill returnera en mindre uppsättning resultat `maxresults` anger du ett icke-nollvärde för parametern när du anropar en av **ListBlobs-metoderna.**

Om en listningsåtgärd returnerar mer än 5000 blobbar, eller om du har angett ett värde för `maxresults` så att liståtgärden returnerar en delmängd av behållare i lagringskontot, returnerar Azure Storage en *fortsättningstoken* med listan över blobbar. En fortsättningstoken är ett ogenomskinligt värde som du kan använda för att hämta nästa uppsättning resultat från Azure Storage.

Kontrollera värdet för fortsättningstoken i koden för att avgöra om den är null. När fortsättningstoken är null är uppsättningen resultat slutförd. Om fortsättningstoken inte är null, anropar du åtgärden igen och skickar in fortsättningstoken för att hämta nästa uppsättning resultat, tills fortsättningstoken är null.

### <a name="filter-results-with-a-prefix"></a>Filtrera resultat med ett prefix

Om du vill filtrera listan över `prefix` behållare anger du en sträng för parametern. Prefixsträngen kan innehålla ett eller flera tecken. Azure Storage returnerar sedan bara de blobbar vars namn börjar med prefixet.

### <a name="return-metadata"></a>Returnera metadata

Om du vill returnera blobmetadata med resultaten anger du **metadatavärdet** för [uppräkningen blobListingDetails.](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) Azure Storage innehåller metadata med varje blob returneras, så du behöver inte anropa en av **FetchAttributes-metoderna** i den här kontexten för att hämta blobmetadata.

### <a name="flat-listing-versus-hierarchical-listing"></a>Platt lista kontra hierarkisk lista

Blobbar i Azure Storage är organiserade i ett platt paradigm, snarare än ett hierarkiskt paradigm (som ett klassiskt filsystem). Du kan dock ordna blobbar i *virtuella kataloger* för att efterlikna en mappstruktur. En virtuell katalog utgör en del av namnet på blobben och indikeras av avgränsartecknet.

Om du vill ordna blobbar i virtuella kataloger använder du ett avgränsare i blobnamnet. Standardtecknet för avgränsare är ett snedstreck (/), men du kan ange vilket tecken som helst som avgränsare.

Om du namnger dina blobbar med hjälp av en avgränsare kan du välja att lista blobbar hierarkiskt. För en hierarkisk listningsåtgärd returnerar Azure Storage alla virtuella kataloger och blobbar under det överordnade objektet. Du kan anropa liståtgärden rekursivt för att gå igenom hierarkin, ungefär som du skulle gå igenom ett klassiskt filsystem programmässigt.

## <a name="use-a-flat-listing"></a>Använda en platt lista

Som standard returnerar en listning blobbar i en platt lista. I en platt lista ordnas inte blobbar efter virtuell katalog.

I följande exempel visas blobbar i den angivna behållaren med hjälp av en platt lista, med en valfri segmentstorlek angiven, och blobnamnet skrivs till ett konsolfönster.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

Provutgången liknar:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Använda en hierarkisk lista

När du anropar en listningsåtgärd hierarkiskt returnerar Azure Storage de virtuella katalogerna och blobbar på den första nivån i hierarkin. Egenskapen [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) för varje virtuell katalog är inställd så att du kan skicka prefixet i ett rekursivt anrop för att hämta nästa katalog.

Om du vill visa blobbar hierarkiskt anger du parametern `useFlatBlobListing` för listningsmetoden till **false**.

I följande exempel visas blobbar i den angivna behållaren med hjälp av en platt lista, med en valfri segmentstorlek angiven, och blobnamnet skrivs till konsolfönstret.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

Provutgången liknar:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Blob-ögonblicksbilder kan inte visas i en hierarkisk listning.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Nästa steg

- [Lista Blobbar](/rest/api/storageservices/list-blobs)
- [Räkna upp Blob-resurser](/rest/api/storageservices/enumerating-blob-resources)
