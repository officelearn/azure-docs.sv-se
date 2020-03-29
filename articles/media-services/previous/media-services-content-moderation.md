---
title: Använd Azure Media Content Moderator för att identifiera eventuellt innehåll för vuxna och racy | Microsoft-dokument
description: Azure Media Content Moderator medieprocessor hjälper till att identifiera potentiella vuxna och racy innehåll i videor.
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
ms.openlocfilehash: 83fe7867a3128ac82597c028452863a1ad681ace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914340"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Använda Azure Media Content Moderator för att identifiera eventuellt innehåll för vuxna och racy 

> [!NOTE]
> **Medieprocessorn** för Azure Media Content Moderator dras tillbaka. För återgivningsdatumet finns i avsnittet [äldre komponenter.](legacy-components.md)

## <a name="overview"></a>Översikt
Med Media Moderator för **Azure Media Moderator** (MP) kan du använda datorstödd moderering för dina videor. Du vill kanske exempelvis identifiera möjligt innehåll som är olämpligt för barn eller olämpligt innehåll i videor och granska innehåll som flaggats av det mänskliga modereringsteamet.

**Azure Media Content Moderator** MP är för närvarande i förhandsversion.

Den här artikeln innehåller information om **Azure Media Content Moderator** och visar hur du använder den med Media Services SDK för .NET.

## <a name="content-moderator-input-files"></a>Indatafiler för innehållsmoderator
Videofiler. För närvarande stöds följande format: MP4, MOV och WMV.

## <a name="content-moderator-output-files"></a>Utdatafiler för innehållsmoderator
Den modererade utdata i JSON-formatet innehåller bildrutor och nyckelrutor. Nyckelrutorna returneras med konfidenspoäng för eventuellt innehåll för vuxna eller racy. De innehåller också en boolesk flagga som anger om en granskning rekommenderas. Flaggan granskningsrekommendation tilldelas värden baserat på de interna tröskelvärdena för vuxen- och racypoäng.

## <a name="elements-of-the-output-json-file"></a>Delar av utdata-JSON-filen

Jobbet skapar en JSON-utdatafil som innehåller metadata om identifierade bilder och nyckelrutor och om de innehåller vuxet eller racy-innehåll.

Utdata JSON innehåller följande element:

### <a name="root-json-elements"></a>Rot JSON-element

| Element | Beskrivning |
| --- | --- |
| version |Versionen av Content Moderator. |
| Tidsskalan |"Fästingar" per sekund av videon. |
| offset |Tidsförskjutningen för tidsstämplar. I version 1.0 av Video API:er är det här värdet alltid 0. Det här värdet kan ändras i framtiden. |
| Framerate |Bildrutor per sekund i videon. |
| bredd |Bredden på utdatavideoramen i bildpunkter.|
| höjd |Höjden på utdatavideoramen, i bildpunkter.|
| totaltUnder- |Varaktigheten av indatavideon, i "fästingar". |
| [Fragment](#fragments-json-elements) |Metadata segmenteras i olika segment som kallas fragment. Varje fragment är ett automatiskt upptäckt skott med start, varaktighet, intervallnummer och händelser. |

### <a name="fragments-json-elements"></a>Fragment JSON element

|Element|Beskrivning|
|---|---|
| start |Starttiden för den första händelsen i "fästingar". |
| varaktighet |Längden på fragmentet, i "fästingar". |
| interval |Intervallet för varje händelsepost i fragmentet, i "fästingar". |
| [Händelser](#events-json-elements) |Varje händelse representerar ett klipp och varje klipp innehåller nyckelrutor som har identifierats och spårats inom den tidsperioden. Det är en rad händelser. Den yttre matrisen representerar ett visst tidsintervall. Den inre matrisen består av 0 eller fler händelser som inträffade vid den tidpunkten.|

### <a name="events-json-elements"></a>Händelser JSON element

|Element|Beskrivning|
|---|---|
| recensionKommend | `true`eller `false` beroende på om **adultScore** eller **racyScore** överskrider de interna trösklarna. |
| vuxenScore | Konfidenspoäng för eventuellt barnförbjudet innehåll, på en skala från 0,00 till 0,99. |
| racyScore (racyScore) | Konfidenspoäng för eventuellt innehåll, på en skala från 0,00 till 0,99. |
| Index | index för ramen på en skala från det första bildruteindexet till det sista bildruteindexet. |
| timestamp | Platsen för ramen i "fästingar". |
| shotIndex (bildIndex) | Indexet för det överordnade skottet. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Snabbstart och provutdata för innehållsmoderering

### <a name="task-configuration-preset"></a>Aktivitetskonfiguration (förinställd)
När du skapar en uppgift med **Azure Media Content Moderator**måste du ange en konfigurationsförinställning. Följande konfigurationsförinställning är bara för moderering av innehåll.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Exempel på .NET-kod

I följande .NET-kodexempel används Media Services .NET SDK för att köra ett jobb för innehållsmodererator. Det tar en media services Asset som den ingång som innehåller videon som ska modereras.
Se [snabbstarten för innehållsmoderator](../../cognitive-services/Content-Moderator/video-moderation-api.md) för den fullständiga källkoden och Visual Studio-projektet.


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

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

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

[Demos för Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Nästa steg

Läs mer om Innehållsmoderatorns [videomoderations- och granskningslösning](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Hämta den fullständiga källkoden och Visual Studio-projektet från [snabbstarten för videomodering](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Lär dig hur du skapar [videorecensioner](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) från modererad utgång och [moderera transkriptioner](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) i .NET.

Kolla in den detaljerade .NET [video moderering och översyn handledning](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
