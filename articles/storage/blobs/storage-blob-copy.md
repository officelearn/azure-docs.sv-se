---
title: Kopiera en blob med .NET-Azure Storage
description: Lär dig hur du kopierar en BLOB i ditt Azure Storage-konto med hjälp av .NET-klient biblioteket.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79135895"
---
# <a name="copy-a-blob-with-net"></a>Kopiera en blob med .NET

Den här artikeln visar hur du kopierar en blob med ett Azure Storage-konto. Det visar också hur du avbryter en asynkron kopierings åtgärd. Exempel koden använder [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>Om att kopiera blobar

När du kopierar en BLOB inom samma lagrings konto är det en synkron åtgärd. När du kopierar mellan-konton är det en asynkron åtgärd. Metoderna [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) och [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) returnerar ett kopierings-ID-värde som används för att kontrol lera status eller avbryta kopierings åtgärden.

Käll-bloben för en kopierings åtgärd kan vara en Block-Blob, en tilläggs-BLOB, en sid-BLOB eller en ögonblicks bild. Om det redan finns en mål-BLOB måste den vara av samma Blob-typ som käll-bloben. Alla befintliga mål-blobar skrivs över. 

Det går inte att ändra mål-bloben medan en kopierings åtgärd pågår. En mål-BLOB kan bara ha en utestående kopierings-BLOB-åtgärd. Med andra ord kan en BLOB inte vara målet för flera väntande kopierings åtgärder.

Hela käll-bloben eller filen kopieras alltid. Det finns inte stöd för att kopiera en mängd byte eller uppsättning block.

När en BLOB kopieras kopieras system egenskaperna till mål-bloben med samma värden.

För alla BLOB-typer kan du kontrol lera egenskapen [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) på mål-bloben för att hämta status för kopierings åtgärden. Den sista blobben kommer att allokeras när kopieringen är klar.

En kopierings åtgärd kan ha följande format:

  - Du kan kopiera en käll-blob till en mål-BLOB med ett annat namn. Mål-bloben kan vara en befintlig blob av samma Blob-typ (block, tillägg eller sida) eller kan vara en ny blob som skapats av kopierings åtgärden.
  - Du kan kopiera en käll-blob till en mål-BLOB med samma namn, vilket i själva verket ersätter mål-bloben. En sådan kopierings åtgärd tar bort alla icke allokerade block och skriver över målets BLOB-metadata.
  - Du kan kopiera en källfil i Azure File Service till en mål-blob. Mål-bloben kan vara en befintlig Block-Blob eller en ny Block-Blob som skapats av kopierings åtgärden. Det finns inte stöd för att kopiera från filer till Page blobbar eller bifogade blobbar.
  - Du kan kopiera en ögonblicks bild över dess bas-blob. Genom att befordra en ögonblicks bild till positionen för bas-bloben kan du återställa en tidigare version av en blob.
  - Du kan kopiera en ögonblicks bild till en mål-BLOB med ett annat namn. Den resulterande mål-bloben är en skrivbar blob och inte en ögonblicks bild.

## <a name="copy-a-blob"></a>Kopiera en BLOB

Om du vill kopiera en BLOB anropar du någon av följande metoder:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

I följande kod exempel hämtas en referens till en blob som skapats tidigare och kopierar den till en ny BLOB i samma behållare:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Avbryt en BLOB-kopiera åtgärd

Om du avbryter en kopierings åtgärd i ett mål-BLOB med längden noll för block-blobbar, bifoga blobbar och sid-blobar. Metadata för mål-bloben kommer dock att ha de nya värdena som kopieras från käll-bloben eller uttryckligen anges i [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) -eller [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) -anropet. Om du vill behålla de ursprungliga metadata från före kopian ska du skapa en ögonblicks bild av `StartCopy` mål `StartCopyAsync`-bloben innan du anropar eller.

När du avbryter en pågående BLOB Copy-åtgärd är mål-blobens [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) inställt på [CopyStatus. abort](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Metoderna [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) och [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) avbryter en pågående BLOB-kopiering och lämnar en mål-BLOB med noll längd och fullständiga metadata.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Nästa steg

Följande avsnitt innehåller information om hur du kopierar blobbar och avbryter pågående kopierings åtgärder med hjälp av Azure REST-API: er.

- [Kopiera blob](/rest/api/storageservices/copy-blob)
- [Avbryt kopiering av BLOB](/rest/api/storageservices/abort-copy-blob)
