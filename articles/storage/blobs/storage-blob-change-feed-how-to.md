---
title: Bearbeta ändrings flöde i Azure Blob Storage (förhands granskning) | Microsoft Docs
description: Lär dig hur du bearbetar ändringar i flödes loggar i ett .NET-klient program
author: normesta
ms.author: normesta
ms.date: 06/18/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: dedf1174e00f5bb75822fb720a592af86121ec2d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691436"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Bearbeta ändrings flöde i Azure Blob Storage (förhands granskning)

Ändra feed innehåller transaktions loggar för alla ändringar som sker i blobbar och blob-metadata i ditt lagrings konto. Den här artikeln visar hur du läser ändringar av flödes poster med hjälp av processor biblioteket för BLOB Change-feed.

Läs mer om ändrings flödet [i ändra feed i Azure Blob Storage (för hands version)](storage-blob-change-feed.md).

> [!NOTE]
> Ändrings flödet finns i en offentlig för hands version och är tillgängligt i regionerna **westcentralus** och **westus2** . Mer information om den här funktionen tillsammans med kända problem och begränsningar finns i [ändra feed-stöd i Azure Blob Storage](storage-blob-change-feed.md). Biblioteket Change feed processor kan ändras mellan nu och när det här biblioteket blir allmänt tillgängligt.

## <a name="get-the-blob-change-feed-processor-library"></a>Hämta processor biblioteket för BLOB Change feed

1. Öppna ett kommando fönster (till exempel: Windows PowerShell).
2. Från projekt katalogen installerar du paketet **Azure. Storage. blobs. Changefeed** NuGet.

```console
dotnet add package Azure.Storage.Blobs.ChangeFeed --source https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json --version 12.0.0-dev.20200604.2
```
## <a name="read-records"></a>Läsa poster

> [!NOTE]
> Ändrings flödet är en oföränderlig och skrivskyddad entitet i ditt lagrings konto. Ett valfritt antal program kan läsa och bearbeta ändrings flödet samtidigt och självständigt med egen bekvämlighet. Poster tas inte bort från ändra feed när ett program läser dem. Läsnings-eller upprepnings statusen för varje förbruknings läsare är oberoende och underhålls endast av ditt program.

Det här exemplet itererar igenom alla poster i ändrings flödet, lägger till dem i en lista och returnerar sedan listan till anroparen.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Det här exemplet skriver ut till konsolen med några värden från varje post i listan. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Återuppta läsning av poster från en sparad position

Du kan välja att spara Läs positionen i ändrings flödet och sedan återuppta sökningen genom posterna vid ett senare tillfälle. Du kan spara Läs positionen genom att hämta Change feed-markören. Markören är en **sträng** och ditt program kan spara strängen på ett sätt som passar ditt programs design (till exempel: till en fil eller databas).

Det här exemplet itererar igenom alla poster i ändrings flödet, lägger till dem i en lista och sparar markören. Listan och markören returneras till anroparen. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Strömmande bearbetning av poster

Du kan välja att bearbeta ändrings flödes poster när de tas emot. Se [specifikationer](storage-blob-change-feed.md#specifications). Vi rekommenderar att du söker efter ändringar varje timme.

Det här exemplet söker regelbundet efter ändringar.  Om det finns ändrings poster bearbetar den här koden dessa poster och sparar ändrings flödes markören. På så sätt om processen har stoppats och sedan startats igen kan programmet använda markören för att återuppta bearbetningen av poster där den senast slutade. I det här exemplet sparas markören till en lokal program konfigurations fil, men programmet kan spara den i alla formulär som är mest begripligt för ditt scenario. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Läser poster inom ett tidsintervall

Du kan läsa poster som ligger inom ett angivet tidsintervall. Det här exemplet itererar igenom alla poster i ändrings flödet som faller mellan 3:00 PM den 2 2017 och 2:00 AM den 7 2019, lägger till dem i en lista och returnerar sedan listan till anroparen.

### <a name="selecting-segments-for-a-time-range"></a>Välja segment för ett tidsintervall

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2017, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 10, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Start tiden som du anger avrundas nedåt till närmaste timme och slut tiden avrundas uppåt till närmaste timme. Det är möjligt att användarna kan se händelser som inträffat före start tiden och efter slut tiden. Det är också möjligt att vissa händelser som inträffar mellan start-och slut tiden inte visas. Det beror på att händelser kan registreras under den timme som tidigare är start tid eller under timmen efter slut tiden.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att ändra flödes loggar. Se [ändra feed i Azure Blob Storage (för hands version)](storage-blob-change-feed.md)
