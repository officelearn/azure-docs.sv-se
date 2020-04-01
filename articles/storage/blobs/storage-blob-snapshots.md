---
title: Skapa och hantera en blob-ögonblicksbild i .NET - Azure Storage
description: Lär dig hur du skapar en skrivskyddad ögonblicksbild av en blob för att säkerhetskopiera blob-data vid en viss tidpunkt.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9bf5eea55002814f461d375b3db43a37fe4f7aa9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474094"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Skapa och hantera en blob-ögonblicksbild i .NET

En ögonblicksbild är en skrivskyddad version av en blob som tas vid en tidpunkt. Ögonblicksbilder är användbara för säkerhetskopiering av blobbar. Den här artikeln visar hur du skapar och hanterar blob-ögonblicksbilder med hjälp av [Azure Storage-klientbiblioteket för .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-blob-snapshots"></a>Om blob-ögonblicksbilder

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

En ögonblicksbild av en blob är identisk med basbloben, förutom att blob-URI:n har ett **DateTime-värde** som läggs till blob URI för att ange den tidpunkt då ögonblicksbilden togs. Om till exempel en sidblobb-URI är `http://storagesample.core.blob.windows.net/mydrives/myvhd`liknar `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`ögonblicksbilden URI .

> [!NOTE]
> Alla ögonblicksbilder delar basblobbens URI. Den enda skillnaden mellan basbloben och ögonblicksbilden är det bifogade **DateTime-värdet.**
>

En blob kan ha valfritt antal ögonblicksbilder. Ögonblicksbilder kvarstår tills de uttryckligen tas bort, vilket innebär att en ögonblicksbild inte kan överleva sin basblob. Du kan räkna upp ögonblicksbilder som är associerade med basbloben för att spåra dina aktuella ögonblicksbilder.

När du skapar en ögonblicksbild av en blob kopieras blobens systemegenskaper till ögonblicksbilden med samma värden. Basblolobens metadata kopieras också till ögonblicksbilden, såvida du inte anger separata metadata för ögonblicksbilden när du skapar den. När du har skapat en ögonblicksbild kan du läsa, kopiera eller ta bort den, men du kan inte ändra den.

Alla lån som är associerade med basbloben påverkar inte ögonblicksbilden. Du kan inte skaffa ett lån på en ögonblicksbild.

En VHD-fil används för att lagra aktuell information och status för en VM-disk. Du kan koppla från en disk från den virtuella datorn eller stänga av den virtuella datorn och sedan ta en ögonblicksbild av dess VHD-fil. Du kan använda den ögonblicksbildfilen senare för att hämta VHD-filen vid den tidpunkten och återskapa den virtuella datorn.

## <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

Om du vill skapa en ögonblicksbild av en blockblob använder du någon av följande metoder:

- [SkapaSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [SkapaSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

I följande kodexempel visas hur du skapar en ögonblicksbild. I det här exemplet anges ytterligare metadata för ögonblicksbilden när den skapas.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>Ta bort ögonblicksbilder

Om du vill ta bort en blob måste du först ta bort alla ögonblicksbilder av den blobben. Du kan ta bort en ögonblicksbild individuellt eller ange att alla ögonblicksbilder ska tas bort när källblobben tas bort. Om du försöker ta bort en blob som fortfarande har ögonblicksbilder uppstår ett fel.

Om du vill ta bort blob-ögonblicksbilder använder du någon av följande blob-borttagningsmetoder och inkluderar [Ta bortSnapshotsOption-uppräkningen.](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption)

- [Ta bort](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

I följande kodexempel visas hur du tar bort en `blockBlob` blob och dess ögonblicksbilder i .NET, där är ett objekt av typen [CloudBlockBlob:][dotnet_CloudBlockBlob]

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Returnera den absoluta URI:n till en ögonblicksbild

I följande kodexempel skapas en ögonblicksbild och den absoluta URI:n för den primära platsen skrivs ut.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Förstå hur ögonblicksbilder samlar på avgifter

Om du skapar en ögonblicksbild, som är en skrivskyddad kopia av en blob, kan det leda till ytterligare datalagringsavgifter till ditt konto. När du utformar ditt program är det viktigt att vara medveten om hur dessa avgifter kan tillkomma så att du kan minimera kostnaderna.

### <a name="important-billing-considerations"></a>Viktiga faktureringsöverväganden

Följande lista innehåller viktiga punkter att tänka på när du skapar en ögonblicksbild.

- Ditt lagringskonto medför avgifter för unika block eller sidor, oavsett om de finns i blobben eller i ögonblicksbilden. Ditt konto medför inga ytterligare avgifter för ögonblicksbilder som är associerade med en blob förrän du uppdaterar bloben som de baseras på. När du har uppdaterat basblobben avviker den från ögonblicksbilderna. När detta inträffar debiteras du för de unika blocken eller sidorna i varje blob eller ögonblicksbild.
- När du ersätter ett block inom en blockblob debiteras blocket senare som ett unikt block. Detta gäller även om blocket har samma block-ID och samma data som det har i ögonblicksbilden. När blocket har bekräftats igen avviker det från sin motsvarighet i alla ögonblicksbilder, och du debiteras för dess data. Detsamma gäller för en sida i en sidblob som uppdateras med identiska data.
- Om du ersätter en blockblob genom att anropa metoden [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]eller [UploadFromByteArray][dotnet_UploadFromByteArray] ersätts alla block i bloben. Om du har en ögonblicksbild som är associerad med den blobben, skiljer sig alla block i basblobben och ögonblicksbilden nu, och du debiteras för alla block i båda blobbar. Detta gäller även om data i basbloben och ögonblicksbilden förblir identiska.
- Azure Blob-tjänsten har inte möjlighet att avgöra om två block innehåller identiska data. Varje block som överförs och bekräftas behandlas som unikt, även om det har samma data och samma block-ID. Eftersom avgifter ackumuleras för unika block är det viktigt att tänka på att uppdatering av en blob som har en ögonblicksbild resulterar i ytterligare unika block och ytterligare avgifter.

### <a name="minimize-cost-with-snapshot-management"></a>Minimera kostnaden med hantering av ögonblicksbilder

Vi rekommenderar att du hanterar dina ögonblicksbilder noggrant för att undvika extra avgifter. Du kan följa de här metodtipsen för att minimera kostnaderna för lagring av ögonblicksbilder:

- Ta bort och återskapa ögonblicksbilder som är associerade med en blob när du uppdaterar blobben, även om du uppdaterar med identiska data, såvida inte programdesignen kräver att du underhåller ögonblicksbilder. Genom att ta bort och återskapa blobens ögonblicksbilder kan du se till att blobben och ögonblicksbilderna inte skiljer sig åt.
- Om du underhåller ögonblicksbilder för en blob undviker du att anropa [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]eller [UploadFromByteArray][dotnet_UploadFromByteArray] för att uppdatera blobben. Dessa metoder ersätter alla block i blobben, vilket gör att basbloben och dess ögonblicksbilder skiljer sig avsevärt. Uppdatera i stället så många block som möjligt med hjälp av metoderna [PutBlock][dotnet_PutBlock] och [PutBlockList.][dotnet_PutBlockList]

### <a name="snapshot-billing-scenarios"></a>Scenarier för fakturering för ögonblicksbilder

Följande scenarier visar hur avgifter ackumuleras för en blockblob och dess ögonblicksbilder.

#### <a name="scenario-1"></a>Scenario 1

I scenario 1 har basbloloben inte uppdaterats efter att ögonblicksbilden togs, så avgifter uppstår endast för unika block 1, 2 och 3.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

I scenario 2 har basbloben uppdaterats, men ögonblicksbilden har inte. Block 3 uppdaterades, och även om det innehåller samma data och samma ID är det inte samma sak som block 3 i ögonblicksbilden. Som ett resultat debiteras kontot för fyra block.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

I scenario 3 har basbloben uppdaterats, men ögonblicksbilden har inte. Block 3 ersattes med block 4 i basbloben, men ögonblicksbilden återspeglar fortfarande block 3. Som ett resultat debiteras kontot för fyra block.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

I scenario 4 har basbloben uppdaterats helt och innehåller inget av dess ursprungliga block. Som ett resultat debiteras kontot för alla åtta unika block. Det här scenariot kan inträffa om du använder en uppdateringsmetod som [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]eller [UploadFromByteArray][dotnet_UploadFromByteArray], eftersom dessa metoder ersätter allt innehåll i en blob.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Nästa steg

- Du kan hitta mer information om hur du arbetar med ögonblicksbilder av virtuella datorer (VM) i [Säkerhetskopiering av Azure ohanterat VM-diskar med inkrementella ögonblicksbilder](../../virtual-machines/windows/incremental-snapshots.md)

- Ytterligare kodexempel med Blob-lagring finns i [Azure-kodexempel](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Du kan hämta ett exempelprogram och köra det, eller bläddra i koden på GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblockblob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
