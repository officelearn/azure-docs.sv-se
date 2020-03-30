---
title: Processändringsfeed i Azure Blob Storage (förhandsversion) | Microsoft-dokument
description: Lär dig hur du bearbetar flödesloggar i ett .NET-klientprogram
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111866"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Bearbeta ändringsfeed i Azure Blob Storage (förhandsversion)

Ändringsfeeden innehåller transaktionsloggar över alla ändringar som sker i blobbar och blobmetadata i ditt lagringskonto. Den här artikeln visar hur du läser ändringsfeedposter med hjälp av blob change feed processor-biblioteket.

Mer information om ändringsflödet finns [i Ändra feed i Azure Blob Storage (Förhandsversion).](storage-blob-change-feed.md)

> [!NOTE]
> Ändringsflödet finns i den offentliga förhandsversionen och finns i **regionerna Westcentralus** och **Westus2.** Mer information om den här funktionen tillsammans med kända problem och begränsningar finns [i Ändra feedstöd i Azure Blob Storage](storage-blob-change-feed.md). Ändringsflödesprocessorbiblioteket kan komma att ändras fram till dess att biblioteket blir allmänt tillgängligt.

## <a name="get-the-blob-change-feed-processor-library"></a>Hämta blob change-processorbiblioteket

1. Lägg till webbadressen `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` i NuGet-paketkällorna i Visual Studio. 

   Mer information finns i [paketkällor](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. Leta reda på **paketet Microsoft.Azure.Storage.Changefeed** i NuGet Package Manager och installera det i projektet. 

   Mer information finns i [Hitta och installera ett paket](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Ansluta till lagringskontot

Tolka anslutningssträngen genom att anropa metoden [CloudStorageAccount.TryParse.](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) 

Skapa sedan ett objekt som representerar Blob Storage i ditt lagringskonto genom att anropa metoden [CloudStorageAccount.CreateCloudBlobClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)

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

## <a name="initialize-the-change-feed"></a>Initiera ändringsflödet

Lägg till följande med hjälp av utdrag överst i kodfilen. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Skapa sedan en instans av klassen **ChangeFeed** genom att anropa metoden **GetContainerReference.** Skicka in namnet på ändringsmatningsbehållaren.

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

## <a name="reading-records"></a>Läsa poster

> [!NOTE]
> Ändringsflödet är en oföränderlig och skrivskyddad entitet i ditt lagringskonto. Valfritt antal program kan läsa och bearbeta ändringsflödet samtidigt och oberoende av egen bekvämlighet. Poster tas inte bort från ändringsflödet när ett program läser dem. Läs- eller iterationstillståndet för varje tidskrävande läsare är oberoende och underhålls endast av ditt program.

Det enklaste sättet att läsa poster är att skapa en instans av klassen **ChangeFeedReader.** 

Det här exemplet iterar igenom alla poster i ändringsflödet och skriver sedan ut några värden från varje post till konsolen. 
 
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

## <a name="resuming-reading-records-from-a-saved-position"></a>Återuppta läsa poster från en sparad position

Du kan välja att spara din läsposition i ändringsflödet och återuppta itererera posterna i framtiden. Du kan när som helst spara tillståndet för din iteration av ändringsflödet med metoden **ChangeFeedReader.SerializeState().** Tillståndet är en **sträng** och ditt program kan spara det tillståndet baserat på programmets design (till exempel till en databas eller en fil).

```csharp
    string currentReadState = processor.SerializeState();
```

Du kan fortsätta att iterera genom poster från det senaste tillståndet genom att skapa **ChangeFeedReader** med metoden **CreateChangeFeedReaderFromPointerAsync.**

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

## <a name="stream-processing-of-records"></a>Strömbearbetning av poster

Du kan välja att bearbeta ändringsflödesposter när de anländer. Se [specifikationer](storage-blob-change-feed.md#specifications).

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

## <a name="reading-records-within-a-time-range"></a>Läsa poster inom ett tidsintervall

Ändringsflödet är ordnat i timsegment baserat på ändringshändelsetiden. Se [specifikationer](storage-blob-change-feed.md#specifications). Du kan läsa poster från ändringsflödessegment som ligger inom ett visst tidsintervall.

Det här exemplet hämtar starttiderna för alla segment. Därefter itererar den genom den listan tills starttiden antingen är bortom tiden för det sista förbrukningssegmentet eller efter sluttiden för det önskade intervallet. 

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

Du kan läsa poster från enskilda segment eller segmentintervall.

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

## <a name="read-records-starting-from-a-time"></a>Läsa poster från och med en tid

Du kan läsa posterna i ändringsflödet från ett startsegment till slutet. I likhet med att läsa poster inom ett tidsintervall kan du lista segmenten och välja ett segment att börja iterera från.

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

I det här exemplet bearbetas [flödesposter från DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) för ett startsegment.

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
> Ett segment av kan ha ändra feed loggar i en eller flera *chunkFilePath*. Vid flera *segmentFilePath* har systemet delat in posterna internt i flera shards för att hantera publiceringsdataflöde. Det garanteras att varje partition i segmentet kommer att innehålla ändringar för ömsesidigt uteslutande blobbar och kan bearbetas oberoende av varandra utan att bryta mot beställningen. Du kan använda klassen **ChangeFeedSegmentShardReader** för att iterera genom poster på fragmentnivå om det är mest effektivt för ditt scenario.

## <a name="next-steps"></a>Nästa steg

Läs mer om ändring av feedloggar. Se [Ändra feed i Azure Blob Storage (förhandsversion)](storage-blob-change-feed.md)
