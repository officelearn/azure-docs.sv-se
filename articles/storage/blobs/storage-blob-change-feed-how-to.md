---
title: Bearbeta ändrings flöde i Azure Blob Storage (förhands granskning) | Microsoft Docs
description: Lär dig hur du bearbetar ändringar i flödes loggar i ett .NET-klient program
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74111866"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Bearbeta ändrings flöde i Azure Blob Storage (förhands granskning)

Ändra feed innehåller transaktions loggar för alla ändringar som sker i blobbar och blob-metadata i ditt lagrings konto. Den här artikeln visar hur du läser ändringar av flödes poster med hjälp av processor biblioteket för BLOB Change-feed.

Läs mer om ändrings flödet [i ändra feed i Azure Blob Storage (för hands version)](storage-blob-change-feed.md).

> [!NOTE]
> Ändrings flödet finns i en offentlig för hands version och är tillgängligt i regionerna **westcentralus** och **westus2** . Mer information om den här funktionen tillsammans med kända problem och begränsningar finns i [ändra feed-stöd i Azure Blob Storage](storage-blob-change-feed.md). Biblioteket Change feed processor kan ändras mellan nu och när det här biblioteket blir allmänt tillgängligt.

## <a name="get-the-blob-change-feed-processor-library"></a>Hämta processor biblioteket för BLOB Change feed

1. I Visual Studio lägger du till URL: en `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` till dina NuGet-paket källor. 

   Mer information finns i [paket källor](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. Leta upp paketet **Microsoft. Azure. Storage. Changefeed** i NuGet Package Manager och installera det i projektet. 

   Mer information finns i [hitta och installera ett paket](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Anslut till lagrings kontot

Parsa anslutnings strängen genom att anropa metoden [CloudStorageAccount. TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) . 

Skapa sedan ett objekt som representerar Blob Storage i ditt lagrings konto genom att anropa metoden [CloudStorageAccount. CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient) .

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>Initiera ändrings flödet

Lägg till följande using-uttryck högst upp i din kod fil. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Skapa sedan en instans av klassen **ChangeFeed** genom att anropa **GetContainerReference** -metoden. Överför namnet på behållaren för ändrings feed.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Läser poster

> [!NOTE]
> Ändrings flödet är en oföränderlig och skrivskyddad entitet i ditt lagrings konto. Ett valfritt antal program kan läsa och bearbeta ändrings flödet samtidigt och självständigt med egen bekvämlighet. Poster tas inte bort från ändra feed när ett program läser dem. Läsnings-eller upprepnings statusen för varje förbruknings läsare är oberoende och underhålls endast av ditt program.

Det enklaste sättet att läsa poster är att skapa en instans av klassen **ChangeFeedReader** . 

Det här exemplet itererar igenom alla poster i ändrings flödet och skriver sedan ut till konsolen med några värden från varje post. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Återupptar läsning av poster från en sparad position

Du kan välja att spara din Läs position i ändrings flödet och återuppta de poster som visas vid ett senare tillfälle. Du kan spara statusen för din iteration av Change-flödet när som helst med hjälp av metoden **ChangeFeedReader. SerializeState ()** . Statusen är en **sträng** och ditt program kan spara det läget baserat på programmets design (till exempel: till en databas eller en fil).

```csharp
    string currentReadState = processor.SerializeState();
```

Du kan fortsätta att söka igenom poster från det senaste läget genom att skapa **ChangeFeedReader** med hjälp av **CreateChangeFeedReaderFromPointerAsync** -metoden.

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Strömmande bearbetning av poster

Du kan välja att bearbeta ändrings flödes poster när de tas emot. Se [specifikationer](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Läser poster inom ett tidsintervall

Ändrings flödet ordnas i Tim segment baserat på ändrings händelse tiden. Se [specifikationer](storage-blob-change-feed.md#specifications). Du kan läsa poster från ändra flödes segment som ligger inom ett angivet tidsintervall.

I det här exemplet hämtas start tiderna för alla segment. Sedan upprepas denna lista tills start tiden är antingen längre än tiden för det sista förbrukade segmentet eller längre än slut tiden för det önskade intervallet. 

### <a name="selecting-segments-for-a-time-range"></a>Välja segment för ett tidsintervall

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Läsa poster i ett segment

Du kan läsa poster från enskilda segment eller segment intervall.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Läs poster som börjar från en tid

Du kan läsa posterna i ändrings flödet från ett start segment till slutet. På samma sätt som när du läser poster inom ett tidsintervall kan du Visa segmenten och välja ett segment som du vill börja iterera från.

Det här exemplet hämtar [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) för det första segmentet som ska bearbetas.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

Det här exemplet bearbetar ändringar av flödes poster från [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) för ett start segment.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Ett segment av kan ha ändringar av flödes loggar i en eller flera *chunkFilePath*. Om det finns flera *chunkFilePath* , har systemet partitionerat posterna i flera Shards för att hantera publicerings data flödet. Det garanterar att varje partition i segmentet innehåller ändringar för ömsesidigt uteslutande blobbar och kan bearbetas separat utan att det bryter ordningen. Du kan använda klassen **ChangeFeedSegmentShardReader** för att iterera genom poster på Shard-nivån om det är mest effektivt för ditt scenario.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att ändra flödes loggar. Se [ändra feed i Azure Blob Storage (för hands version)](storage-blob-change-feed.md)
