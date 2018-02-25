---
title: "Skapa en skrivskyddad ögonblicksbild av en blobb i Azure Storage | Microsoft Docs"
description: "Lär dig hur du skapar en ögonblicksbild av en blob att säkerhetskopiera blob-data vid en given tidpunkt. Förstå hur ögonblicksbilder debiteras och hur du använder dem för att minimera kapacitet avgifter."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 04/11/2017
ms.author: tamram
ms.openlocfilehash: cba28ada79ea806ead4ae9165abba2dc4e04f001
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-blob-snapshot"></a>Skapa en blob-ögonblicksbild

En ögonblicksbild är en skrivskyddad version av en blob som utförs i en punkt i tiden. Ögonblicksbilder är användbara för att säkerhetskopiera blobbar. När du har skapat en ögonblicksbild, läsa, kopiera eller ta bort den, men du kan inte ändra den.

En ögonblicksbild av en blob är identisk med dess grundläggande blob, förutom att blob-URI: N har en **DateTime** värde som läggs till blob-URI om du vill ange när ögonblicksbilden togs. Till exempel om en sida blob-URI är `http://storagesample.core.blob.windows.net/mydrives/myvhd`, ögonblicksbilden URI liknar `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Alla ögonblicksbilder dela grundläggande blob-URI. Är den enda skillnaden mellan grundläggande blob och ögonblicksbilden den tillagda **DateTime** värde.
>

En blob kan ha valfritt antal ögonblicksbilder. Ögonblicksbilder sparas tills de uttryckligen tas bort. En ögonblicksbild inte sträcker sig längre än dess grundläggande blob. Du kan räkna upp ögonblicksbilder kopplade till grundläggande blob att spåra din aktuella ögonblicksbilder.

När du skapar en ögonblicksbild av en blob kopieras blobens Systemegenskaper till ögonblicksbilden med samma värden. Grundläggande blob metadata kopieras också till ögonblicksbilden, om du inte anger separat metadata för ögonblicksbilden när du skapar den.

Alla lån som är associerade med grundläggande blob påverkar inte ögonblicksbilden. Du kan inte hämta ett lån på en ögonblicksbild.

En VHD-fil används för att spara aktuella information och status för en virtuell disk. Du kan koppla bort en disk från den virtuella datorn eller stänga av den virtuella datorn och sedan ta en ögonblicksbild av dess VHD-filen. Du kan använda ögonblicksbild filen senare för att hämta VHD-filen då i tid och återskapa den virtuella datorn.

Om kryptering för lagring-tjänsten (SSE) är aktiverat för det lagringskonto där blobben finns, krypteras alla ögonblicksbilder av blobben i vila.

## <a name="create-a-snapshot"></a>Skapa en ögonblicksbild
Följande kodexempel visar hur du skapar en ögonblicksbild med hjälp av den [Azure Storage-klientbibliotek för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Det här exemplet anger ytterligare metadata för ögonblicksbilden när den skapas.

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
Kopieringsåtgärder som rör blobbar och ögonblicksbilder följer dessa regler:

* Du kan kopiera en ögonblicksbild över dess grundläggande blob. Du kan återställa en tidigare version av en blob genom att befordra en ögonblicksbild till positionen för den grundläggande blobben. Ögonblicksbild fortfarande, men grundläggande blob skrivs över med en skrivbar kopia av ögonblicksbilden.
* Du kan kopiera en ögonblicksbild till en mål-blob med ett annat namn. Det resulterande mål-blobben är en skrivbar blob och inte en ögonblicksbild.
* När en käll-blob kopieras kopieras alla ögonblicksbilder på käll-blob inte till målet. När en mål-blob skrivs över med en kopia, bevaras eventuella ögonblicksbilder som är kopplade till den ursprungliga mål-blobben.
* När du skapar en ögonblicksbild av en blockblobb kopieras även blobens allokerat Blockeringslista till ögonblicksbilden. Alla ogenomförda block kopieras inte.

## <a name="specify-an-access-condition"></a>Ange ett villkor för åtkomst
När du anropar [CreateSnapshotAsync][dotnet_CreateSnapshotAsync], du kan ange ett villkor för åtkomst så att ögonblicksbilden skapas endast om ett villkor uppfylls. Om du vill ange ett villkor för åtkomst av [AccessCondition] [ dotnet_AccessCondition] parameter. Om det angivna villkoret uppfylls inte ögonblicksbilden har inte skapats och Blob-tjänsten returnerar statuskod [HTTPStatusCode][dotnet_HTTPStatusCode]. PreconditionFailed.

## <a name="delete-snapshots"></a>Ta bort ögonblicksbilder
Du kan inte ta bort en blob med ögonblicksbilder om ögonblicksbilderna tas också bort. Du kan ta bort en ögonblicksbild individuellt eller ange att alla ögonblicksbilder tas bort när käll-blob har tagits bort. Om du försöker ta bort en blobb som fortfarande har ögonblicksbilder, uppstår ett fel.

Följande kodexempel visar hur du tar bort en blob och dess ögonblicksbilder i .NET, där `blockBlob` är ett objekt av typen [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Ögonblicksbilder med Azure Premium-lagring
När du använder ögonblicksbilder med Premium-lagring, gäller följande regler:

* Det maximala antalet ögonblicksbilder per sidblob i ett premiumlagringskonto är 100. Om denna gräns har överskridits ögonblicksbild Blob-åtgärd returnerar felkoden 409 (`SnapshotCountExceeded`).
* Du kan ta en ögonblicksbild av en sidblob i ett premiumlagringskonto var 10: e minut. Om denna kurs har överskridits ögonblicksbild Blob-åtgärd returnerar felkoden 409 (`SnapshotOperationRateExceeded`).
* Du kan använda kopiera Blob-åtgärden för att kopiera en ögonblicksbild till en annan sidblob i kontot för att läsa en ögonblicksbild. Mål-blob för kopieringen får inte ha några befintliga ögonblicksbilder. Om mål-blob har ögonblicksbilder och sedan kopiera Blob-åtgärd returnerar felkoden 409 (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Returnera en absolut URI till en ögonblicksbild
Den här C#-kodexempel skapar en ögonblicksbild och skriver ut absolut URI för den primära platsen.

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

## <a name="understand-how-snapshots-accrue-charges"></a>Förstå hur ögonblicksbilder påförs kostnader
Skapa en ögonblicksbild som är en skrivskyddad kopia av en blob, kan leda till ytterligare data lagringskostnader för volymer till ditt konto. När du skapar ditt program, är det viktigt att känna till hur dessa avgifter kan tillkomma så att du kan minimera kostnaderna.

### <a name="important-billing-considerations"></a>Tänk på följande fakturering
Följande lista innehåller viktiga saker att tänka på när du skapar en ögonblicksbild.

* Ditt lagringskonto debiteras för unik block eller sidor, oavsett om de är i blob eller i ögonblicksbilden. Ditt konto leder inte till ytterligare avgifter för ögonblicksbilder kopplade till en blob förrän du har uppdaterat blob som de är baserad på. När du har uppdaterat grundläggande blob skiljer sig från dess ögonblicksbilder. När detta inträffar debiteras du för unik block eller sidor i varje blob eller en ögonblicksbild.
* När du ersätter ett block inom en blockblobb debiteras som blockerar senare som en unik block. Detta gäller även om blocket har samma block-ID och samma data eftersom den har i ögonblicksbilden. När blocket strävar igen och det avviker från sin motsvarighet i en ögonblicksbild och du kommer att debiteras för sina data. Detsamma gäller för en sida i en sidblob som uppdateras med identiska data.
* Ersätta en blockblobb genom att anropa den [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], eller [UploadFromByteArray] [ dotnet_UploadFromByteArray] metoden ersätter alla block i blob. Om du har en ögonblicksbild som är associerade med blobben alla block i grundläggande blob och ögonblicksbild nu avvika och du kommer att debiteras för alla block i båda blobbar. Detta gäller även om data i grundläggande blob och ögonblicksbilden vara identiska.
* Azure Blob-tjänsten har inte ett sätt att avgöra om två block innehåller identiska data. Varje block som har överförts och verkställs behandlas som unika, även om den har samma data och samma block-ID. Eftersom det tillkomma avgifter för unik block, är det viktigt att beakta som uppdaterar en blob som har en ögonblicksbild resulterar i ytterligare unika block och ytterligare avgifter.

### <a name="minimize-cost-with-snapshot-management"></a>Minimera kostnad med hantering av ögonblicksbilder

Vi rekommenderar att du hanterar din ögonblicksbilder noggrant för att undvika extra kostnader. Du kan följa dessa rekommendationer för att minimera kostnaderna genom lagring av ögonblicksbilder av din:

* Ta bort och återskapa ögonblicksbilder kopplade till en blob när du uppdaterar blob, även om du uppdaterar med identiska data om din program-designen kräver att du behåller ögonblicksbilder. Genom att ta bort och återskapa det blob ögonblicksbilder, kan du se till att blob och ögonblicksbilder inte sig.
* Om du underhåller ögonblicksbilder för en blob och undvika anropar [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], eller [UploadFromByteArray] [ dotnet_UploadFromByteArray] att uppdatera blob. Dessa metoder ersätta alla block i blob orsakar din grundläggande blob och dess ögonblicksbilder till avvika avsevärt. Uppdatera i stället det minsta antalet block med hjälp av den [PutBlock] [ dotnet_PutBlock] och [PutBlockList] [ dotnet_PutBlockList] metoder.

### <a name="snapshot-billing-scenarios"></a>Ögonblicksbild fakturering scenarier
Följande scenarier visas hur debiteringar görs för en blockblobb och dess ögonblicksbilder.

**Scenario 1**

I scenario 1 har grundläggande blob inte uppdaterats när ögonblicksbilden togs, så avgifter tas ut endast unika block 1, 2 och 3.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Scenario 2**

I scenario 2 grundläggande blob har uppdaterats, men ögonblicksbilden har inte. Block 3 har uppdaterats och även om den innehåller samma data och samma ID, den är inte samma som blockerar 3 i ögonblicksbilden. Därför debiteras kontot för fyra block.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Scenario 3**

I scenario 3 grundläggande blob har uppdaterats, men ögonblicksbilden har inte. Block 3 ersattes med block 4 i basen blob, men ögonblicksbilden visar block 3. Därför debiteras kontot för fyra block.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Scenario 4**

I scenariot 4 grundläggande blob har uppdaterats helt och innehåller inga av dess ursprungliga block. Därför debiteras kontot för alla åtta unika block. Den här situationen kan uppstå om du använder en metod för att uppdatera som [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream], eller [UploadFromByteArray][dotnet_UploadFromByteArray], eftersom dessa metoder Ersätt hela innehållet i en blob.

![Azure Storage-resurser](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Nästa steg

* Du hittar mer information om hur du arbetar med ögonblicksbilder av virtuella datorer (VM)-disk i [säkerhetskopiera Azure ohanterade Virtuella diskar med inkrementell ögonblicksbilder](../../virtual-machines/windows/incremental-snapshots.md)

* Ytterligare kodexempel med Blob storage finns [Azure kodexempel](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Du kan hämta ett exempelprogram och kör den, eller bläddra koden på GitHub.

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