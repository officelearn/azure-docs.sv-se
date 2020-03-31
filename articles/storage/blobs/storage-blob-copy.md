---
title: Kopiera en blob med .NET - Azure Storage
description: Lär dig hur du kopierar en blob i ditt Azure Storage-konto med .NET-klientbiblioteket.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135895"
---
# <a name="copy-a-blob-with-net"></a>Kopiera en blob med .NET

Den här artikeln visar hur du kopierar en blob med ett Azure Storage-konto. Den visar också hur du avbryter en asynkron kopiering. Exempelkoden använder [Azure Storage-klientbiblioteket för .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>Om att kopiera blobbar

När du kopierar en blob i samma lagringskonto är det en synkron åtgärd. När du kopierar mellan konton är det en asynkron åtgärd. [Metoderna StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) och [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) returnerar ett kopierings-ID-värde som används för att kontrollera status eller avbryta kopieringen.

Källbloben för en kopiering kan vara en blockblob, en tilläggsblob, en sidblob eller en ögonblicksbild. Om målbloben redan finns måste den vara av samma blobtyp som källblobben. Alla befintliga målblob kommer att skrivas över. 

Målbloben kan inte ändras medan en kopiering pågår. En målblob kan bara ha en utestående kopia blob-åtgärd. Med andra ord kan en blob inte vara målet för flera väntande kopieringsåtgärder.

Hela källblobben eller -filen kopieras alltid. Det går inte att kopiera ett antal byte eller blockuppsättningar.

När en blob kopieras kopieras dess systemegenskaper till målblobben med samma värden.

För alla blobtyper kan du kontrollera egenskapen [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) på målbloben för att få status för kopieringen. Den slutliga blobben kommer att bekräftas när kopian är klar.

En kopiering kan ha något av följande former:

  - Du kan kopiera en källblob till en målblob med ett annat namn. Målbloben kan vara en befintlig blob av samma blobtyp (block, tillägg eller sida) eller kan vara en ny blob som skapas av kopieringen.
  - Du kan kopiera en källblob till en målblob med samma namn och effektivt ersätta målbloben. En sådan kopiering tar bort alla okrediterade block och skriver över målblobbens metadata.
  - Du kan kopiera en källfil i Azure File-tjänsten till en målblob. Målbloben kan vara en befintlig blockblob eller vara en ny blockblob som skapats av kopieringen. Det går inte att kopiera från filer till sidblobar eller lägga till blobbar.
  - Du kan kopiera en ögonblicksbild över dess basblob. Genom att befordra en ögonblicksbild till positionen för basbloben kan du återställa en tidigare version av en blob.
  - Du kan kopiera en ögonblicksbild till en målblob med ett annat namn. Den resulterande målbloben är en skrivbar blob och inte en ögonblicksbild.

## <a name="copy-a-blob"></a>Kopiera en blob

Om du vill kopiera en blob anropar du någon av följande metoder:

 - [StartKopia](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

I följande kodexempel hämtas en referens till en blob som skapats tidigare och kopieras till en ny blob i samma behållare:

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

## <a name="abort-a-blob-copy-operation"></a>Avbryta en blob-kopieringsåtgärd

Om du avbryter en kopieringsåtgärd resulterar det i en målblobb med noll längd för blockblobar, tilläggsblobbar och sidblobar. Metadata för målblobben kommer dock att få de nya värdena kopierade från källblobben eller anges explicit i [StartCopy-](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) eller [StartCopyAsync-anropet.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) Om du vill behålla de ursprungliga metadata från före kopian `StartCopy` `StartCopyAsync`gör du en ögonblicksbild av målbloben innan du ringer eller .

När du avbryter en pågående blob-kopieringsåtgärd anges målblobben [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) till [CopyStatus.Aborterad](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Metoderna [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) och [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) avbryter en pågående blob-kopiering och lämnar en målblobb med noll längd och fullständiga metadata.

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

Följande avsnitt innehåller information om hur du kopierar blobbar och avbryter pågående kopieringsåtgärder med hjälp av Azure REST API:er.

- [Kopiera blob](/rest/api/storageservices/copy-blob)
- [Avbryt kopia blob](/rest/api/storageservices/abort-copy-blob)
