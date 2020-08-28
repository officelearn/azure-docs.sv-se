---
title: Använd Azure Media Content Moderator för att identifiera eventuell vuxen och vågat innehåll | Microsoft Docs
description: Azure Media Content Moderator Media-processorn hjälper till att identifiera potentiellt vuxna och vågat innehåll i videor.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.custom: devx-track-csharp
ms.openlocfilehash: d30949327f3025c06d4c98670494809c486631a3
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022215"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Använd Azure Media Content Moderator för att identifiera eventuell vuxen och vågat innehåll 

> [!NOTE]
> **Azure Media Content moderator** Media-processorn kommer att dras tillbaka. Information om återställnings datumet finns i avsnittet [äldre komponenter](legacy-components.md) .

## <a name="overview"></a>Översikt
Med **Azure Media Content moderator** Media-processorn (MP) kan du använda dator redigering för dina videor. Du vill kanske exempelvis identifiera möjligt innehåll som är olämpligt för barn eller olämpligt innehåll i videor och granska innehåll som flaggats av det mänskliga modereringsteamet.

**Azure Media Content moderator** MP är för närvarande en för hands version.

Den här artikeln innehåller information om  **Azure Media Content moderator** och visar hur du använder det med Media Services SDK för .net.

## <a name="content-moderator-input-files"></a>Content Moderator indatafiler
Videofiler. För närvarande stöds följande format: MP4, MOV och WMV.

## <a name="content-moderator-output-files"></a>Content Moderator utdatafiler
De kontrollerade utdata i JSON-formatet innehåller automatiskt identifierade bilder och nyckel rutor. Nyckel rutorna returneras med förtroende poäng för möjlig vuxen eller vågat innehåll. De innehåller också en boolesk flagga som anger om en granskning rekommenderas. Flaggan för gransknings rekommendationen tilldelas värden baserat på de interna tröskelvärdena för vuxna och vågat resultat.

## <a name="elements-of-the-output-json-file"></a>Element i JSON-filen för utdata

Jobbet skapar en JSON-utdatafil som innehåller metadata om identifierade bilder och nyckel rutor och om de innehåller vuxen eller vågat innehåll.

Utdata-JSON innehåller följande element:

### <a name="root-json-elements"></a>JSON-element för rot

| Element | Beskrivning |
| --- | --- |
| version |Versionen av Content Moderator. |
| tidsplan |"Tickar" per sekund för videon. |
| offset |Tidsförskjutningen för tidsstämplar. I version 1,0 av video-API: er, är det här värdet alltid 0. Det här värdet kan ändras i framtiden. |
| ram |Bildrutor per sekund i videon. |
| bredd |Bredden på video rutan för utdata i bild punkter.|
| mankhöjd |Höjden på video rutan för utdata i bild punkter.|
| totalDuration |Varaktigheten för inmatad video i "ticker". |
| [fragment](#fragments-json-elements) |Metadata delas upp i olika segment som kallas fragment. Varje fragment är en automatiskt identifierad bild med start, varaktighet, intervall nummer och händelse (r). |

### <a name="fragments-json-elements"></a>Fragmenterar JSON-element

|Element|Beskrivning|
|---|---|
| start |Start tiden för den första händelsen i "ticks". |
| varaktighet |Längden på fragmentet i "tickas". |
| interval |Intervallet för varje händelse post i fragmentet, i "ticker". |
| [planering](#events-json-elements) |Varje händelse representerar ett klipp och varje klipp innehåller nyckel rutor som har identifierats och spårats inom den tids perioden. Det är en matris med händelser. Den yttre matrisen representerar ett visst tidsintervall. Den inre matrisen består av 0 eller fler händelser som inträffade vid den tidpunkten.|

### <a name="events-json-elements"></a>Händelser JSON-element

|Element|Beskrivning|
|---|---|
| reviewRecommended | `true` eller `false` beroende på om **AdultScore** eller **racyScore** överskrider de interna tröskelvärdena. |
| adultScore | Tillförlitlighets poängen för potentiellt vuxen innehåll, på en skala på 0,00 till 0,99. |
| racyScore | Tillförlitlighets poängen för möjligt vågat-innehåll, på en skala på 0,00 till 0,99. |
| index | index för ramen i en skala från det första ram indexet till det sista ram indexet. |
| timestamp | Placeringen av ramen i "ticker". |
| shotIndex | Index för den överordnade bilden. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Snabb start för innehålls redigering och exempel på utdata

### <a name="task-configuration-preset"></a>Uppgifts konfiguration (förval)
När du skapar en aktivitet med **Azure Media Content moderator**måste du ange en konfigurations för inställning. Följande konfigurations för inställning är bara för innehålls moderator.

```json
{
    "version":"2.0"
}
```

### <a name="net-code-sample"></a>Exempel på .NET-kod

I följande .NET-kod exempel används Media Services .NET SDK för att köra ett Content Moderator jobb. Det tar en medie tjänst till gång som indata som innehåller videon som ska förvaras.
Se [snabb start för Content moderator video](../../cognitive-services/Content-Moderator/video-moderation-api.md) för den fullständiga käll koden och Visual Studio-projektet.


```csharp
/// <summary>
/// Run the Content Moderator job on the designated Asset from local file or blob storage
/// </summary>
/// <param name="asset"></param>
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
               asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                                                  mp, configuration,
                                                  TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
                                             AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                .First();
            Console.WriteLine(string.Format("{0}\t{1}",
                                            DateTime.Now,
                                            jobQuery.State));
            Thread.Sleep(10000);
        }
        while (jobQuery.State != JobState.Finished &&
               jobQuery.State != JobState.Error &&
               jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
                           CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
                          error.Code,
                          error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

Läs mer om den fullständiga käll koden och Visual Studio-projektet i [Content moderator video snabb start](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### <a name="json-output"></a>JSON-utdata

Följande exempel på en Content Moderator JSON-utdata trunkerades.

> [!NOTE]
> Plats för en nyckel bild ruta i sekunder = tidsstämpel/tids skala

```json
{
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
        {
            "start": 0,
            "duration": 18000
        },
        {
            "start": 18000,
            "duration": 3600,
            "interval": 3600,
            "events": [
                [
                    {
                        "reviewRecommended": false,
                        "adultScore": 0.00001,
                        "racyScore": 0.03077,
                        "index": 5,
                        "timestamp": 18000,
                        "shotIndex": 0
                    }
                ]
            ]
        },
        {
            "start": 18386372,
            "duration": 119149,
            "interval": 119149,
            "events": [
                [
                    {
                        "reviewRecommended": true,
                        "adultScore": 0.00000,
                        "racyScore": 0.91902,
                        "index": 5085,
                        "timestamp": 18386372,
                        "shotIndex": 62
                    }
                ]
            ]
        }
    ]
}
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Översikt över Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure-medieanalys demonstrationer](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att Content Moderator [videokontrollanten och se](../../cognitive-services/Content-Moderator/video-moderation-human-review.md)hur du går igenom lösningen.

Hämta den fullständiga käll koden och Visual Studio-projektet från [videoinspelnings snabb](../../cognitive-services/Content-Moderator/video-moderation-api.md)starten. 

Lär dig hur du genererar [video granskningar](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) från dina kontrollerade utdata och [måttliga avskrifter](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) i .net.

Kolla in den detaljerade [självstudien om .net-videoinspelning och granskning](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
