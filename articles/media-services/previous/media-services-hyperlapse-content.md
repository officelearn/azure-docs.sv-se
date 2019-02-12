---
title: Videostabilisera mediefiler med Azure Media Hyperlapse | Microsoft Docs
description: Azure Media Hyperlapse skapar smooth timelapse-video från första personen eller åtgärd kamera innehåll. Det här avsnittet visar hur du använder Media Indexer.
services: media-services
documentationcenter: ''
author: asolanki
manager: johndeu
editor: ''
ms.assetid: 37d54db6-9cf3-4ae9-b3c6-0d29c744e965
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/10/2019
ms.author: adsolank
ms.openlocfilehash: c3ef977e7e8742f4d6de0f85d2d1684aa7eaa887
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993766"
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Videostabilisera mediefiler med Azure Media Hyperlapse

> [!NOTE]
> Kommer snart att dras tillbaka den här funktionen för förhandsgranskning i Azure Media Services. Från och med 19 December 2018, kommer Media Services inte längre göra ändringar eller förbättringar i Media Hyperlapse. Den 29 mars 2019 blir den indragna och inte längre tillgänglig.

Azure Media Hyperlapse är en Media Processor (MP) som skapar smooth timelapse-video från första personen eller åtgärd kamera innehåll.  Den molnbaserade på samma nivå till [Microsoft Research skrivbord Hyperlapse Pro och telefon Hyperlapse Mobile](https://aka.ms/hyperlapse), Microsoft Hyperlapse för Azure Media Services använder enorma skalan för bearbetning till Media för Azure Media Services plattformen för att vågrätt skala och parallellisera bulk Hyperlapse-bearbetning.

> [!IMPORTANT]
> Microsoft Hyperlapse fungerar bäst på första personen innehåll med en glidande kamera. Även om fortfarande övervakningskameror kan fortfarande fungerar kan inte prestanda och kvalitet på Azure Media Hyperlapse Mediebearbetare garanteras för andra typer av innehåll.
> 
> 

Ett Azure Media Hyperlapse-jobb tar som indata en MP4 eller MOV WMV tillgångsfil tillsammans med en konfigurationsfil som anger vilka bildrutor i videon ska vara timelapse- och vilken hastighet (t.ex. första 10 000 ramar till 2 x).  Utdata är en stabila och timelapse-återgivning av indatavideo.

## <a name="hyperlapse-an-asset"></a>Videostabilisering en tillgång
Först behöver du överföra önskade indatafilen till Azure Media Services.  Mer information om vilka begrepp som används med att ladda upp och hantera innehåll i [innehållshantering artikeln](media-services-portal-vod-get-started.md).

### <a id="configuration"></a>Konfigurationen förinställning för Videostabilisering
När det är ditt innehåll i Media Services-kontot, måste du konstruera configuration förinställning.  I följande tabell beskrivs de användardefinierade fält:

| Fält | Beskrivning |
| --- | --- |
| StartFrame |Ramens som Microsoft Hyperlapse-bearbetning ska börja. |
| NumFrames |Antal bildrutor att bearbeta |
| Hastighet |Faktorer med vilken hastighet indatavideon ska ha. |

Följande är ett exempel på en kompatibel konfigurationsfil i XML- och JSON:

**XML-förinställning:**
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>
```

**Förinställningen för JSON:**
```json
    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }
```

### <a id="sample_code"></a> Microsoft Hyperlapse med AMS .NET SDK
Följande metod överför en mediefil som en tillgång och skapar ett jobb med Azure Media Hyperlapse Mediebearbetare.

> [!NOTE]
> Du bör redan ha en CloudMediaContext i scope med namnet ”kontexten” för den här koden ska fungera.  Mer information om detta finns i [innehållshantering artikeln](media-services-dotnet-get-started.md).
> 
> [!NOTE]
> Strängargumentet ”hyperConfig” förväntas vara en kompatibel konfiguration förinställda i JSON eller XML, enligt beskrivningen ovan.
> 
> 

```csharp
        static bool RunHyperlapseJob(string input, string output, string hyperConfig)
        {
            // create asset with input file
            IAsset asset = context
            .Assets
            .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

            // grab instances of Azure Media Hyperlapse MP
            IMediaProcessor mp = context
            .MediaProcessors
            .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

            // create Job with Hyperlapse task
            IJob job = context
            .Jobs
            .Create(String.Format("Hyperlapse {0}", input));

            if (String.IsNullOrEmpty(hyperConfig))
            {
            // config cannot be empty
            return false;
            }

            hyperConfig = File.ReadAllText(hyperConfig);

            ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
            mp,
            hyperConfig,
            TaskOptions.None);
            hyperlapseTask.InputAssets.Add(asset);
            hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
            AssetCreationOptions.None);

            job.Submit();

            // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {

            IJob jobQuery = null;
            do
            {
                var progressContext = context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                DateTime.Now,
                jobQuery.State,
                jobQuery.Tasks[0].Progress));
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }
```

### <a id="file_types"></a>Filtyper som stöds
* MP4
* MOV
* WMV

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Medieanalys-demonstrationer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

