---
title: Identifiera rörelser med Azure Media Analytics | Microsoft-dokument
description: Med Azure Media Motion Detector-medieprocessorn (MP) kan du effektivt identifiera intressanta delar i en annars lång och händelselös video.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: f4c021531a4d04bf16e5dbee4172952433f675d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913012"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Identifiera rörelser med Azure Media Analytics

> [!NOTE]
> **Medieprocessorn** för Azure Media Motion Detector dras tillbaka. För återgivningsdatumet finns i avsnittet [äldre komponenter.](legacy-components.md)
 
## <a name="overview"></a>Översikt

**Med Azure Media Motion Detector-medieprocessorn** (MP) kan du effektivt identifiera intressanta delar i en annars lång och händelselös video. Rörelsedetektering kan användas på statiska kamerabilder för att identifiera delar av videon där rörelse uppstår. Den genererar en JSON-fil som innehåller en metadata med tidsstämplar och det begränsningsområde där händelsen inträffade.

Riktad mot säkerhet videoflöden, denna teknik kan kategorisera rörelse i relevanta händelser och falska positiva såsom skuggor och ljusförändringar. Detta gör att du kan generera säkerhetsvarningar från kameraflöden utan att vara spammad med oändliga irrelevanta händelser, samtidigt som du kan extrahera stunder av intresse från långa övervakningsvideor.

**Azure Media Motion Detector** MP är för närvarande i förhandsversion.

Den här artikeln innehåller information om **Azure Media Motion Detector** och visar hur du använder den med Media Services SDK för .NET

## <a name="motion-detector-input-files"></a>Indatafiler för rörelsedetektor
Videofiler. För närvarande stöds följande format: MP4, MOV och WMV.

## <a name="task-configuration-preset"></a>Aktivitetskonfiguration (förinställd)
När du skapar en uppgift med **Azure Media Motion Detector**måste du ange en konfigurationsförinställning. 

### <a name="parameters"></a>Parametrar
Du kan använda följande parametrar:

| Namn | Alternativ | Beskrivning | Default |
| --- | --- | --- | --- |
| känslighetNivå |Sträng:"låg", "medium", "hög" |Ställer in känslighetsnivån där rörelser rapporteras. Justera detta för att justera antalet falska positiva identifieringar. |"medium" |
| ramSamplingVärde |Positivt heltal |Anger hur ofta algoritmen körs. 1 är lika med varje ram, 2 betyder varannan bildruta, och så vidare. |1 |
| detectLightChange |Booleskt:'true', 'false' |Anger om ljusförändringar rapporteras i resultaten |"Falskt" |
| mergeTimeThreshold |Xs-tid: TT:mm:ss<br/>Exempel: 00:00:03 |Anger tidsfönstret mellan rörelsehändelser där 2 händelser kombineras och rapporteras som 1. |00:00:00 |
| detectionZones |En matris med detektionszoner:<br/>- Detection Zone är en matris med 3 eller fler punkter<br/>- Punkt är ett x och y koordinat från 0 till 1. |Beskriver listan över polygonala detekteringszoner som ska användas.<br/>Resultaten rapporteras med zonerna som ett ID, där det första är "id":0 |En zon, som täcker hela ramen. |

### <a name="json-example"></a>JSON exempel

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Utdatafiler för rörelsedetektor
Ett rörelseidentifieringsjobb returnerar en JSON-fil i utdatatillgången, som beskriver rörelseaviseringarna och deras kategorier i videon. Filen innehåller information om hur länge och hur länge den rörelse som upptäckts i videon.

Api:et för rörelsedetektorer ger indikatorer när det finns objekt i rörelse i en fast bakgrundsvideo (till exempel en övervakningsvideo). Rörelsedetektorn är utbildad för att minska falsklarm, såsom belysning och skuggförändringar. Aktuella begränsningar av algoritmerna inkluderar mörkerseende videor, halvtransparent objekt och små objekt.

### <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Delar av utdata-JSON-filen
> [!NOTE]
> I den senaste versionen har Utdata-JSON-formatet ändrats och kan representera en brytande förändring för vissa kunder.
> 
> 

I följande tabell beskrivs element i utdatafilen JSON.

| Element | Beskrivning |
| --- | --- |
| version |Detta refererar till versionen av video-API:et. Den nuvarande versionen är 2. |
| Tidsskalan |"Fästingar" per sekund av videon. |
| offset |Tidsförskjutningen för tidsstämplar i "fästingar". I version 1.0 av Video API:er är detta alltid 0. I framtida scenarier som vi stöder kan det här värdet ändras. |
| Framerate |Bildrutor per sekund i videon. |
| bredd, höjd |Refererar till videons bredd och höjd i pixlar. |
| start |Starttidsstämpeln i "fästingar". |
| varaktighet |Längden på händelsen, i "fästingar". |
| interval |Intervallet för varje post i händelsen, i "fästingar". |
| händelser |Varje händelsefragment innehåller den rörelse som har identifierats inom den tidsperioden. |
| typ |I den aktuella versionen är detta alltid "2" för generisk rörelse. Den här etiketten ger video-API:er flexibiliteten att kategorisera rörelse i framtida versioner. |
| regionId |Som förklarats ovan kommer detta alltid att vara 0 i denna version. Den här etiketten ger Video API flexibiliteten att hitta rörelse i olika regioner i framtida versioner. |
| Regioner |Refererar till det område i videon där du bryr dig om rörelse. <br/><br/>-"id" representerar regionområdet – i den här versionen finns det bara ett, ID 0. <br/>-"typ" representerar formen på den region du bryr dig om för rörelse. För närvarande stöds "rektangel" och "polygon".<br/> Om du har angett "rektangel" har regionen dimensioner i X, Y, Bredd och Höjd. X- och Y-koordinaterna representerar de övre XY-koordinaterna för regionen i en normaliserad skala på 0,0 till 1,0. Bredden och höjden representerar regionens storlek i en normaliserad skala på 0,0 till 1,0. I den aktuella versionen är X, Y, Bredd och Höjd alltid fixerade vid 0, 0 och 1, 1. <br/>Om du har angett "polygon" har regionen dimensioner i punkter. <br/> |
| Fragment |Metadata segmenteras i olika segment som kallas fragment. Varje fragment innehåller en start, varaktighet, intervallnummer och händelser. Ett fragment utan händelser innebär att ingen rörelse upptäcktes under starttiden och varaktigheten. |
| parentes [] |Varje hakparentes representerar ett intervall i händelsen. Tomma parenteser för det intervallet innebär att ingen rörelse upptäcktes. |
| platser |Den nya posten under händelser visar den plats där rörelsen inträffade. Detta är mer specifikt än identifieringszonerna. |

Följande JSON-exempel visar utdata:

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>Begränsningar
* De indatavideoformat som stöds inkluderar MP4, MOV och WMV.
* Rörelsedetektering är optimerad för stationära bakgrundsvideor. Algoritmen fokuserar på att minska falsklarm, till exempel ljusförändringar och skuggor.
* Vissa rörelser kanske inte upptäcks på grund av tekniska utmaningar. till exempel mörkerseendevideor, halvgenomskinliga objekt och små objekt.

## <a name="net-sample-code"></a>EXEMPELkod för .NET

Följande program visar hur du:

1. Skapa en tillgång och ladda upp en mediefil till tillgången.
2. Skapa ett jobb med en videorörelseidentifieringsuppgift baserat på en konfigurationsfil som innehåller följande json-förinställning: 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
            }
            }
    ```

3. Ladda ner utdata JSON filer. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation enligt beskrivningen i [Media Services-utvecklingen med .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exempel

```csharp

using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace VideoMotionDetection
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

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
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Bloggen Azure Media Services rörelsedetektor](https://azure.microsoft.com/blog/motion-detector-update/)

[Översikt över Azure Media Services Analytics](media-services-analytics-overview.md)

[Demos för Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

