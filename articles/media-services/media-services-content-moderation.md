---
title: "Använda Azure Media innehåll kontrollanten för att identifiera möjliga innehåll för vuxna och dyr | Microsoft Docs"
description: "Video avbrottsmoderering hjälper dig att identifiera potentiella innehåll för vuxna och dyr i videor."
services: media-services
documentationcenter: 
author: sanjeev3
manager: mikemcca
editor: 
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/06/2018
ms.author: sajagtap
ms.openlocfilehash: 43e22e553b5243d6edc413c7a667089793f95396
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Använda Azure Media innehåll kontrollanten för att identifiera möjliga innehåll för vuxna och dyr

## <a name="overview"></a>Översikt
Den **Azure Media innehåll kontrollant** medieprocessor (HP) kan du använda datorn stödd avbrottsmoderering för dina videor. Du kanske vill identifiera möjliga vuxna och dyr innehåll i videor och granska flaggade innehållet från ditt mänsklig måtta.

Den **Azure Media innehåll kontrollant** MP är för närvarande under förhandsgranskning.

Den här artikeln innehåller information om **Azure Media innehåll kontrollant** och visar hur du använder det med Media Services SDK för .NET.

## <a name="content-moderator-input-files"></a>Innehåll kontrollant inkommande filer
Videofiler. För närvarande stöds följande format: MP4, MOV och WMV.

## <a name="content-moderator-output-files"></a>Kontrollant utdata innehållsfiler
Kontrollerade utdata i JSON-format innehåller detekterade bilderna och nyckelrutor. Nyckelrutorna returneras med förtroende resultat för möjliga innehåll för vuxna eller dyr. De omfattar också en boolesk flagga som indikerar om ett omdöme rekommenderas. Granska rekommendation flaggan tilldelas värden baserat på de interna tröskelvärdena för vuxna och dyr resultat.

## <a name="elements-of-the-output-json-file"></a>Element i JSON-filen för utdata

Jobbet producerar en JSON-utdata-fil som innehåller metadata om identifierade bilderna nyckelrutor och om de innehåller innehåll för vuxna eller dyr.

Utdata JSON innehåller följande element:

### <a name="root-json-elements"></a>Roten JSON-element

| Element | Beskrivning |
| --- | --- |
| version |Versionen av innehåll kontrollanten. |
| tidsrymd |”Tick” per sekund i videon. |
| förskjutning |Tidsförskjutningen för tidsstämplar. Det här värdet kommer alltid att 0 version 1.0 av API: er för Video. Det här värdet kan ändras i framtiden. |
| ramhastighet |Bildrutor per sekund av videon. |
| Bredd |Bredden på utdata video ramen, i bildpunkter.|
| Höjd |Höjden på utdata video ramen, i bildpunkter.|
| totalDuration |Varaktighet för inmatningen video i ”tick”. |
| [fragment](#fragments-json-elements) |Metadata är chunked upp till olika segment som kallas fragment. Varje fragment är ett detekterade som med en start, varaktighet, antalet och händelse(r). |

### <a name="fragments-json-elements"></a>Fragment JSON-element

|Element|Beskrivning|
|---|---|
| start |Starttiden för den första händelsen i ”tick”. |
| Varaktighet |Längden på ett fragment, i ”tick”. |
| interval |Intervallet för varje händelsepost i avsnittet i ”tick”. |
| [händelser](#events-json-elements) |Varje händelse representerar ett klipp och varje klippet innehåller nyckelrutor identifieras och spåras inom den varaktigheten. Det är en matris av händelser. Yttre matrisen representerar ett tidsintervall. Den interna matrisen består av 0 eller fler händelser som skedde vid den punkten i tid.|

### <a name="events-json-elements"></a>Händelser JSON-element

|Element|Beskrivning|
|---|---|
| reviewRecommended | `true`eller `false` beroende på om den **adultScore** eller **racyScore** överstiger de interna tröskelvärdena. |
| adultScore | Förtroende resultat för möjliga vuxet innehåll på en skala från 0.00 0,99. |
| racyScore | Förtroende resultat för möjliga dyr innehåll på en skala från 0.00 0,99. |
| index | index för ramen på en skala från den första bildrutan index till den senaste ram index. |
| tidsstämpel | Platsen för ram i ”tick”. |
| shotIndex | Indexet för den överordnade tog. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Innehåll Avbrottsmoderering quickstart och exempel utdata

### <a name="task-configuration-preset"></a>Uppgiftskonfigurationen (förinställda)
När du skapar en uppgift med **Azure Media innehåll kontrollant**, måste du ange en konfiguration förinställning. Följande konfiguration förinställningen gäller bara för innehåll måtta.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>.NET-kodexempel

Följande kodexempel för .NET använder Media Services .NET SDK för att köra ett jobb innehåll kontrollanten. Det tar ett media services tillgång som indata som innehåller video till dämpas.
Finns det [innehåll kontrollant video quickstart](../cognitive-services/Content-Moderator/video-moderation-api.md) för fullständig källkoden och Visual Studio-projekt.


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

För fullständig källkoden och Visual Studio-projekt, ta en titt på [innehåll kontrollant video quickstart](../cognitive-services/Content-Moderator/video-moderation-api.md).

### <a name="json-output"></a>JSON-utdata

Följande exempel visar en innehåll kontrollant JSON-utdata har trunkerats.

> [!NOTE]
> Platsen för en keyframe i sekunder = tidsstämpel/tidsrymd

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


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Media Analytics demonstrationer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Nästa steg

Mer information om innehåll kontrollant [video avbrottsmoderering och granska lösningen](../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Hämta den fullständiga källkoden och Visual Studio-projekt från den [video avbrottsmoderering quickstart](../cognitive-services/Content-Moderator/video-moderation-api.md). 

Lär dig hur du skapar [video granskar](../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) från din kontrollerad utdata och [måttlig betyg](../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) i .NET.

Kolla in detaljerade .NET [avbrottsmoderering och granska självstudiekurs](../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
