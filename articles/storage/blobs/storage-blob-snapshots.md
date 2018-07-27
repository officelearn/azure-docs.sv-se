---
title: Skapa en skrivskyddad ögonblicksbild av en blob i Azure Storage | Microsoft Docs
description: Lär dig hur du skapar en ögonblicksbild av en blob för säkerhetskopiering av blob-data vid en given tidpunkt. Förstå hur ögonblicksbilder faktureras och hur du använder dem för att minimera kostnader för kapacitet.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: 6fa223ffcbc70b2f17649645df3daed22746edd0
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264039"
---
# <a name="create-a-blob-snapshot"></a>Skapa en blob-ögonblicksbild

En ögonblicksbild är en skrivskyddad version av en blob som utförs en gång i tid. Ögonblicksbilder är användbara för att säkerhetskopiera blobar. När du har skapat en ögonblicksbild, du kan läsa, kopiera eller ta bort den, men du kan inte ändra den.

En ögonblicksbild av en blob är identiska med dess grundläggande blob, förutom att blob-URI: N har en **DateTime** värdet tillagt blob-URI som visar den tid då ögonblicksbilden togs. Till exempel om en sida blob-URI: N är `http://storagesample.core.blob.windows.net/mydrives/myvhd`, ögonblicksbilden URI liknar `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Alla ögonblicksbilder dela grundläggande blob-URI. Är den enda skillnaden mellan grundläggande blob och ögonblicksbilden den tillagda **DateTime** värde.
>

En blob kan ha valfritt antal ögonblicksbilder. Ögonblicksbilder bevaras tills de uttryckligen tas bort. En ögonblicksbild kan inte sträcker sig längre än dess grundläggande blob. Du kan räkna upp ögonblicksbilder som är associerade med grundläggande blobben att spåra din aktuella ögonblicksbilder.

När du skapar en ögonblicksbild av en blob kopieras blobens Systemegenskaper till ögonblicksbilden med samma värden. Den grundläggande blobbmetadata kopieras även till ögonblicksbilden, om du inte anger separat metadata för ögonblicksbilden när du skapar den.

Alla lån som är associerade med den grundläggande blobben påverkar inte ögonblicksbilden. Du kan skaffa en leasing för en ögonblicksbild.

En VHD-fil används för att lagra den aktuella informationen och status för en VM-disk. Du kan koppla bort en disk från den virtuella datorn eller stänga av den virtuella datorn och sedan ta en ögonblicksbild av dess VHD-filen. Du kan använda ögonblicksbildfilen senare för att hämta VHD-filen från den punkten i tid och återskapa den virtuella datorn.

## <a name="create-a-snapshot"></a>Skapa en ögonblicksbild
I följande kodexempel visar hur du skapar en ögonblicksbild med hjälp av den [Azure Storage-klientbiblioteket för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Det här exemplet anger ytterligare metadata för ögonblicksbilden när den skapas.

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
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
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

## <a name="copy-snapshots"></a>Kopiera ögonblicksbilder
Kopia av åtgärder som rör blobar och ögonblicksbilder Följ de här reglerna:

* Du kan kopiera en ögonblicksbild över dess grundläggande blob. Du kan återställa en tidigare version av en blob genom att främja en ögonblicksbild till positionen för den grundläggande blobben. Ögonblicksbild kvar, men grundläggande blob skrivs över med en skrivbar kopia av ögonblicksbilden.
* Du kan kopiera en ögonblicksbild till en mål-blob med ett annat namn. Den resulterande målblobben är en skrivbar blob och inte en ögonblicksbild.
* När en källblob kopieras kopieras alla ögonblicksbilder av källbloben inte till målet. När en målblobben skrivas över med en kopia, förblir eventuella ögonblicksbilder som är associerade med den ursprungliga målblobben intakta.
* När du skapar en ögonblicksbild av en blockblob, kopieras även blobens allokerade Blockeringslista till ögonblicksbilden. Alla ogenomförda block kopieras inte.

## <a name="specify-an-access-condition"></a>Ange ett villkor för åtkomst
När du anropar [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], du kan ange ett villkor för åtkomst så att ögonblicksbilden har skapats endast om ett villkor uppfylls. Om du vill ange ett villkor för åtkomst, använda den [AccessCondition] [ dotnet_AccessCondition] parametern. Om det angivna villkoret inte uppfylls, ögonblicksbilden har inte skapats och Blob-tjänsten returnerar statuskoden [HTTPStatusCode][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>Ta bort ögonblicksbilder
Du kan inte ta bort en blob med ögonblicksbilder, såvida inte ögonblicksbilder tas också bort. Du kan ta bort en ögonblicksbild individuellt eller ange att alla ögonblicksbilder tas bort när källbloben tas bort. Om du försöker ta bort en blob som fortfarande har ögonblicksbilder, uppstår ett fel.

I följande kodexempel visas hur du tar bort en blob och dess ögonblicksbilder i .NET, där `blockBlob` är ett objekt av typen [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Ögonblicksbilder med Azure Premium Storage
När du använder ögonblicksbilder med Premium Storage, gäller följande regler:

* Det maximala antalet ögonblicksbilder per sidblob i ett premiumlagringskonto är 100. Om den gränsen överskrids, ta ögonblicksbild av Blob-åtgärden returnerar felkoden 409 (`SnapshotCountExceeded`).
* Du kan ta en ögonblicksbild av en sidblob i ett premiumlagringskonto var tionde minut. Om den frekvensen överskrids ta ögonblicksbild av Blob-åtgärden returnerar felkoden 409 (`SnapshotOperationRateExceeded`).
* Du kan använda kopiering av Blob-åtgärden för att läsa en ögonblicksbild för att kopiera en ögonblicksbild till en annan sidblob i kontot. Mål-blob för att kopieringen får inte ha alla befintliga ögonblicksbilder. Om målblobben har ögonblicksbilder och kopiering av Blob-åtgärden returnerar felkoden 409 (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Returnera en absolut URI till en ögonblicksbild
Den här C#-kodexempel skapas en ögonblicksbild och skriver ut absolut URI för den primära platsen.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Förstå hur ögonblicksbilder påföras kostnader
Skapa en ögonblicksbild som är en skrivskyddad kopia av en blob, kan det resultera i ytterligare data lagringskostnader till ditt konto. När ditt program är det viktigt att känna till hur dessa kostnader kan påförs så att du kan minimera kostnaderna.

### <a name="important-billing-considerations"></a>Viktigt för debitering
Följande lista innehåller viktiga saker att tänka på när du skapar en ögonblicksbild.

* Ditt lagringskonto tillkommer avgifter för unika block eller sidor, oavsett om de är i blob eller i ögonblicksbilden. Ditt konto inga ytterligare avgifter för ögonblicksbilder som är associerade med en blob förrän du har uppdaterat den blob som de är baserad på. När du har uppdaterat grundläggande blob skiljer sig från dess ögonblicksbilder. När detta händer debiteras du för unika block eller sidor i varje blob eller en ögonblicksbild.
* När du ersätter ett block i en blockblob debiteras därefter detta block som ett unikt block. Detta gäller även om blocket har samma block-ID och samma data som de har i ögonblicksbilden. När blockeringen strävar igen, det skiljer sig från domänkontrollanterna i alla ögonblicksbilder och du debiteras för sina data. Detsamma gäller för en sida i en sidblobb som uppdateras med identiska data.
* Ersätta en blockblob genom att anropa den [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream] [ dotnet_UploadFromStream], eller [UploadFromByteArray] [ dotnet_UploadFromByteArray] metoden ersätter alla block i blobben. Om du har en ögonblicksbild som är associerade med den blobben alla block i den grundläggande och ögonblicksbild nu avvika och du kommer att debiteras för alla block i båda blobbar. Detta gäller även om data i den grundläggande blobben och ögonblicksbilden stämmer överens.
* Azure Blob-tjänsten har inte ett sätt att avgöra om två block innehåller identiska data. Varje block som har laddats upp och allokerat behandlas som unik, även om den har samma data och samma block-ID. Eftersom avgifter tillkommer för unika block, är det viktigt att tänka på som uppdaterar en blob som har en ögonblicksbild resulterar i ytterligare unika block och ytterligare avgifter.

### <a name="minimize-cost-with-snapshot-management"></a>Minimera kostnaderna med hantering av ögonblicksbilder

Vi rekommenderar att du hanterar din ögonblicksbilder noggrant för att undvika extra kostnader. Du kan följa dessa bästa metoder för att minimera kostnader lagring av dina ögonblicksbilder:

* Ta bort och återskapa ögonblicksbilder som är associerade med en blob när du uppdaterar blob, även om du uppdaterar med identiska data, såvida inte utformningen av programmet måste du underhålla ögonblicksbilder. Genom att ta bort och återskapa den blob-ögonblicksbilder, kan du se till att den och ögonblicksbilder inte sig.
* Om du underhåller ögonblicksbilder för en blob och undvika anropa [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], eller [UploadFromByteArray] [ dotnet_UploadFromByteArray] att uppdatera blobben. Dessa metoder ersätta alla block i den blob som orsakar din grundläggande blob och dess ögonblicksbilder till avviker markant. I stället uppdatera det lägsta antalet block med hjälp av den [PutBlock] [ dotnet_PutBlock] och [PutBlockList] [ dotnet_PutBlockList] metoder.

### <a name="snapshot-billing-scenarios"></a>Ögonblicksbild fakturering scenarier
Följande scenarier visar hur kostnader påförs för en blockblob och dess ögonblicksbilder.

**Scenario 1**

I scenario 1 har grundläggande blob inte uppdaterats när ögonblicksbilden togs, så debiteras du bara för unika block 1, 2 och 3.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Scenario 2**

I scenario 2 grundläggande blob har uppdaterats, men ögonblicksbilden har inte. Blockera 3 har uppdaterats, och även om den innehåller samma data och samma ID är inte densamma som blockerar 3 i ögonblicksbilden. Därför kan debiteras konton som för fyra block.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Scenario 3**

I scenario 3 grundläggande blob har uppdaterats, men ögonblicksbilden har inte. Blockera 3 ersattes med block 4 i grundläggande blob, men ögonblicksbilden fortfarande visar block 3. Därför kan debiteras konton som för fyra block.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Scenario 4**

I scenariot 4 grundläggande blob har uppdaterats helt och innehåller ingen av dess ursprungliga block. Därför debiteras kontot för alla åtta unika block. Den här situationen kan uppstå om du använder en uppdateringsmetod som [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [ UploadFromStream][dotnet_UploadFromStream], eller [UploadFromByteArray][dotnet_UploadFromByteArray], eftersom dessa metoder Ersätt hela innehållet för en blob.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Nästa steg

* Du hittar mer information om hur du arbetar med ögonblicksbilder av virtuella datorer (VM)-disk i [säkerhetskopiera Azure ohanterade Virtuella datordiskar med inkrementella ögonblicksbilder](../../virtual-machines/windows/incremental-snapshots.md)

* Ytterligare kodexempel med Blob storage finns i [kodexempel för Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Du kan ladda ned ett exempelprogram och köra den, eller bläddra i koden på GitHub.

[dotnet_AccessCondition]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.accesscondition.aspx
[dotnet_CloudBlockBlob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx
[dotnet_CreateSnapshotAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.createsnapshotasync.aspx
[dotnet_HTTPStatusCode]: https://msdn.microsoft.com/library/system.net.httpstatuscode(v=vs.110).aspx
[dotnet_PutBlockList]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblocklist.aspx
[dotnet_PutBlock]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblock.aspx
[dotnet_UploadFromByteArray]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfrombytearray.aspx
[dotnet_UploadFromFile]: https://msdn.microsoft.com/library/azure/mt705654.aspx
[dotnet_UploadFromStream]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstream.aspx
[dotnet_UploadText]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadtext.aspx