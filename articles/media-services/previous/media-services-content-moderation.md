---
title: Använda Azure Media Content Moderator för att identifiera möjliga vuxet eller olämpligt innehåll | Microsoft Docs
description: Videomoderering hjälper dig att identifiera potentiellt vuxet eller olämpligt innehåll i videor.
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
ms.openlocfilehash: eb16f5e1e72e5a9379ad530ab9677adba2ccbbcd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465685"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Använda Azure Media Content Moderator för att identifiera möjliga vuxet eller olämpligt innehåll 

## <a name="overview"></a>Översikt
Den **Azure Media Content Moderator** mediebearbetare (MP) kan du använda datorstödd moderering för dina videor. Du vill kanske exempelvis identifiera möjligt innehåll som är olämpligt för barn eller olämpligt innehåll i videor och granska innehåll som flaggats av det mänskliga modereringsteamet.

Den **Azure Media Content Moderator** MP förhandsvisas just nu.

Den här artikeln innehåller information om **Azure Media Content Moderator** och visar hur du använder det med Media Services SDK för .NET.

## <a name="content-moderator-input-files"></a>Content Moderator indatafiler
Videofiler. För närvarande stöds följande format: MP4 MOV och WMV.

## <a name="content-moderator-output-files"></a>Content Moderator utdatafiler
Kontrollerad utdata i JSON-format innehåller upptäcks automatiskt skärmbilder och nyckelbildrutor. Nyckelrutorna returneras med tillförsikt poäng för möjliga vuxet eller olämpligt innehåll. De innehåller också en boolesk flagga som anger om en granskning rekommenderas. Granska rekommendation flaggan tilldelas värden baserat på de interna tröskelvärdena för vuxet och vågat innehåll poäng.

## <a name="elements-of-the-output-json-file"></a>Element i JSON-filen för utdata

Jobbet producerar en JSON-utdata-fil som innehåller metadata om identifierade skärmbilder och nyckelrutor och om de innehålla vuxet eller olämpligt innehåll.

Utdata JSON innehåller följande element:

### <a name="root-json-elements"></a>Rot JSON-element

| Element | Beskrivning |
| --- | --- |
| version |Versionen av Content Moderator. |
| tidsskalan |”Ticken” per sekund av videon. |
| offset |Tidsförskjutningen för tidsstämplar. I version 1.0 av Video-API: er, kommer det här värdet alltid vara 0. Det här värdet kan ändras i framtiden. |
| ramhastighet |Bildrutor per sekund i videon. |
| Bredd |Bredden på utdata video ramen i bildpunkter.|
| Höjd |Höjden på utdata video ramen i bildpunkter.|
| totalDuration |Varaktigheten för indata video i ”ticken”. |
| [fragment](#fragments-json-elements) |Metadata är segmentvis upp i olika segment som kallas fragment. Varje fragment är en bild som upptäcks automatiskt med en start, varaktighet, antalet och händelse(r). |

### <a name="fragments-json-elements"></a>Fragment JSON-element

|Element|Beskrivning|
|---|---|
| start |Starttiden för den första händelsen i ”ticken”. |
| varaktighet |Längden på ett fragment, i ”ticken”. |
| interval |Intervallet för varje händelsepost i avsnittet i ”ticken”. |
| [händelser](#events-json-elements) |Varje händelse representerar ett klipp och varje klippet innehåller nyckelrutor upptäckt och spåras inom den varaktigheten. Det är en matris med händelser. Den yttre matrisen representerar ett visst tidsintervall. Den inre matrisen består av 0 eller fler händelser som inträffade vid den tidpunkten.|

### <a name="events-json-elements"></a>Händelser som JSON-element

|Element|Beskrivning|
|---|---|
| reviewRecommended | `true` eller `false` beroende på om den **adultScore** eller **racyScore** överskrider de interna tröskelvärdena. |
| adultScore | Förtroendepoäng för möjliga vuxet innehåll på en skala från 0,00 0,99. |
| racyScore | Förtroendepoäng för möjliga olämpligt innehåll på en skala från 0,00 0,99. |
| index | index för ramen på en skala från den första bildrutan index till den senaste ramens index. |
| tidsstämpel | Platsen för ramens i ”ticken”. |
| shotIndex | Index för överordnat som. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Innehåll Moderering Snabbstart och exempel utdata

### <a name="task-configuration-preset"></a>Uppgiftskonfiguration (förinställning)
När du skapar en uppgift med **Azure Media Content Moderator**, måste du ange en förinställning för konfigurationen. Följande configuration förinställningen är bara för innehållsmoderering.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>.NET-kodexempel

Följande kodexempel för .NET använder Media Services .NET SDK för att köra ett jobb för Content Moderator. Det tar en media services tillgången som indata som innehåller videon för att vara kontrollerad.
Se den [Content Moderator video Snabbstart](../../cognitive-services/Content-Moderator/video-moderation-api.md) för den fullständiga källkoden och Visual Studio-projektet.


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
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Medieanalys-demonstrationer](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Nästa steg

Läs mer om Content Moderator [video moderering och granska lösningen](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Hämta den fullständiga källkoden och Visual Studio-projekt från den [videomoderering snabbstarten](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Lär dig hur du skapar [videon går igenom](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) från din kontrollerad utdata, och [måttlig avskrifter](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) i .NET.

Kolla in de detaljerade .NET [moderering och granska självstudievideo](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
