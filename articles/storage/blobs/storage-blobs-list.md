---
title: Lista blobbar med .NET-Azure Storage
description: Lär dig hur du listar blobbar i en behållare i ditt Azure Storage-konto med hjälp av .NET-klient biblioteket. Kod exempel visar hur du listar blobbar i en platt lista eller hur du listar blobbar hierarkiskt, som om de var ordnade i kataloger eller mappar.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b51016b1b73b51cd1f1ec827f05fc9a93dd4cb38
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650715"
---
# <a name="list-blobs-with-net"></a>Lista blobbar med .NET

När du listar blobbar från din kod kan du ange ett antal alternativ för att hantera hur resultat returneras från Azure Storage. Du kan ange antalet resultat som ska returneras i varje uppsättning resultat och sedan hämta de efterföljande uppsättningarna. Du kan ange ett prefix för att returnera blobbar vars namn börjar med den tecken eller strängen. Du kan också lista blobbar i en plan struktur eller hierarkiskt. En hierarkisk lista returnerar blobbar som om de var ordnade i mappar. 

Den här artikeln visar hur du listar blobbar med hjälp av [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-blob-listing-options"></a>Förstå List alternativ för BLOB

Om du vill visa en lista över blobarna i ett lagrings konto anropar du någon av följande metoder:

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Om du vill visa en lista över blobarna i en behållare, anropa någon av följande metoder:

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Överlagringarna för dessa metoder ger ytterligare alternativ för att hantera hur blobbar returneras av list åtgärden. De här alternativen beskrivs i följande avsnitt.

### <a name="manage-how-many-results-are-returned"></a>Hantera hur många resultat som returneras

Som standard returnerar en List åtgärd upp till 5000 resultat i taget. Om du vill returnera en mindre uppsättning resultat anger du ett värde som inte är noll för parametern `maxresults` när du anropar en av **ListBlobs** -metoderna.

Om en List åtgärd returnerar mer än 5000 blobbar, eller om du har angett ett värde för `maxresults` så att List åtgärden returnerar en delmängd av behållare i lagrings kontot, Azure Storage returnerar en *fortsättnings-token* med listan över blobbar. En fortsättnings-token är ett ogenomskinligt värde som du kan använda för att hämta nästa uppsättning resultat från Azure Storage.

I din kod kontrollerar du värdet för fortsättnings-token för att avgöra om det är null. När tilläggs-token är null slutförs uppsättningen av resultat. Om tilläggs-token inte är null anropar du List åtgärden igen och skickar i fortsättnings-token för att hämta nästa uppsättning resultat, tills den fortsatta token är null.

### <a name="filter-results-with-a-prefix"></a>Filtrera resultat med ett prefix

Om du vill filtrera listan över behållare anger du en sträng för parametern `prefix`. Prefixlängden kan innehålla ett eller flera tecken. Azure Storage returnerar sedan bara blobbar vars namn börjar med prefixet.

### <a name="return-metadata"></a>Returnera metadata

Om du vill returnera BLOB-metadata med resultaten anger du värdet för **metadata** för [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) -uppräkningen. Azure Storage innehåller metadata med varje blob som returneras, så du behöver inte anropa någon av **FetchAttributes** -metoderna i den här kontexten för att hämta BLOB-metadata.

### <a name="flat-listing-versus-hierarchical-listing"></a>Platt lista jämfört med hierarkisk lista

Blobbar i Azure Storage ordnas i ett plant paradigm i stället för ett hierarkiskt paradigm (som ett klassiskt fil system). Du kan dock organisera blobbar i *virtuella kataloger* för att efterlikna en mappstruktur. En virtuell katalog utgör en del av namnet på blobben och anges med avgränsnings tecken.

Använd ett avgränsnings tecken i BLOB-namnet för att organisera blobbar i virtuella kataloger. Standard avgränsnings tecken är ett snedstreck (/), men du kan ange valfritt tecken som avgränsare.

Om du namnger Blobbarna med hjälp av en avgränsare kan du välja att lista blobbar hierarkiskt. För en hierarkisk List åtgärd returnerar Azure Storage alla virtuella kataloger och blobbar under det överordnade objektet. Du kan anropa List åtgärden rekursivt för att bläddra i hierarkin, på liknande sätt som du skulle gå igenom ett klassiskt fil system program mässigt.

## <a name="use-a-flat-listing"></a>Använda en platt lista

Som standard returnerar en List åtgärd blobbar i en platt lista. I en platt lista organiseras blobbar inte av en virtuell katalog.

I följande exempel visas blobarna i den angivna behållaren med hjälp av en platt lista, med en valfri segment storlek angiven och skriver BLOB-namnet i ett konsol fönster.

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

Exempel resultatet ser ut ungefär så här:

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

När du anropar en List åtgärd hierarkiskt, returnerar Azure Storage virtuella kataloger och blobbar på den första nivån i hierarkin. Egenskapen [prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) för varje virtuell katalog är inställd så att du kan skicka prefixet i ett rekursivt anrop för att hämta nästa katalog.

Om du vill visa blobar hierarkiskt anger du parametern `useFlatBlobListing` för List metoden till **falsk**.

I följande exempel visas blobarna i den angivna behållaren med hjälp av en platt lista, med en valfri segment storlek angiven och skriver BLOB-namnet i konsol fönstret.

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

Exempel resultatet ser ut ungefär så här:

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
> BLOB-ögonblicksbilder kan inte listas i en hierarkisk List åtgärd.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Nästa steg

- [Lista blobbar](/rest/api/storageservices/list-blobs)
- [Räkna upp BLOB-resurser](/rest/api/storageservices/enumerating-blob-resources)
