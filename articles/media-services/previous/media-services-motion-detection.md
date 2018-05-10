---
title: Identifiera rörelser med Azure Media Analytics | Microsoft Docs
description: Azure Media rörelsedetektor medieprocessor (HP) kan du effektivt identifiera avsnitt av intresse i ett annat långa och primärdomänkontrollant video.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 8488b968fe2ab823479d70a98ba86be97b28f67d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="detect-motions-with-azure-media-analytics"></a>Identifiera rörelser med Azure Media Analytics
## <a name="overview"></a>Översikt
Den **Azure Media rörelsedetektor** medieprocessor (HP) kan du effektivt identifiera avsnitt av intresse i ett annat långa och primärdomänkontrollant video. Rörelseidentifiering kan användas på statisk övervakningskameror för att identifiera avsnitt av videon där rörelse inträffar. Den genererar en JSON-fil som innehåller en metadata med tidsstämplar och den omgivande region där händelsen inträffade.

Riktad mot säkerhet video feeds kan den här tekniken kategorisera rörelse i relevanta händelser och falska positiva identifieringar som skuggor och andra ändringar. På så sätt kan du skapa säkerhetsaviseringar från kameran feeds utan som skräppost med oändlig irrelevanta händelser när kunna extrahera stund intressanta från lång övervakning videor.

Den **Azure Media rörelsedetektor** MP är för närvarande under förhandsgranskning.

Den här artikeln innehåller information om **Azure Media rörelsedetektor** och visar hur du använder det med Media Services SDK för .NET

## <a name="motion-detector-input-files"></a>Rörelse detektor inkommande filer
Videofiler. För närvarande stöds följande format: MP4, MOV och WMV.

## <a name="task-configuration-preset"></a>Uppgiftskonfigurationen (förinställda)
När du skapar en uppgift med **Azure Media rörelsedetektor**, måste du ange en konfiguration förinställning. 

### <a name="parameters"></a>Parametrar
Du kan använda följande parametrar:

| Namn | Alternativ | Beskrivning | Standard |
| --- | --- | --- | --- |
| sensitivityLevel |Sträng: låg, 'medium', 'hög' |Anger hur känsliga på vilka rörelser rapporteras. Justera det här alternativet om du vill justera antalet falska positiva identifieringar. |-medium' |
| frameSamplingValue |Positivt heltal |Anger frekvensen vid vilken algoritm körs. 1 är lika med varje ram, 2: alla andra ramen och så vidare. |1 |
| detectLightChange |Booleskt: 'true', 'false' |Anger om lätta ändringar rapporteras i resultaten |'False' |
| mergeTimeThreshold |Tid för xs:: Mm: ss<br/>Exempel: 00:00:03 |Anger tidsfönstret mellan rörelse händelser där 2 händelser är kombineras och rapporteras som 1. |00:00:00 |
| detectionZones |En matris med identifiering zoner:<br/>-Identifiering zonen är en matris med 3 eller fler punkter<br/>-Platsen är en x och y koordinaten från 0 till 1. |Beskrivs i listan över identifiering av polygona zoner som ska användas.<br/>Resultatet rapporteras med zonerna som ett ID med först en som ”id”: 0 |Zon, som omfattar hela ramen. |

### <a name="json-example"></a>JSON-exempel

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

## <a name="motion-detector-output-files"></a>Videofiler detektor utdata
Ett jobb för identifiering av rörelse returnerar en JSON-fil i utdatatillgången som beskriver rörelse aviseringar och deras kategorier i videon. Filen innehåller information om starttid och varaktighet rörelse upptäcktes i videon.

Rörelse detektor API innehåller indikatorer när det finns objekt i rörelse i en fast bakgrund video (till exempel övervakning av video). Den rörelsedetektor utbildade att minska falsklarm, till exempel ljus och shadow ändringar. Aktuella begränsningar av algoritmerna är natt vision videor, delvis transparenta objekt och små objekt.

### <a id="output_elements"></a>Element i JSON-filen för utdata
> [!NOTE]
> I den senaste versionen utdata-JSON-formatet har ändrats och kan utgöra en ny ändring för vissa kunder.
> 
> 

I följande tabell beskrivs element i utdata-JSON-fil.

| Element | Beskrivning |
| --- | --- |
| Version |Detta refererar till versionen av Video-API. Den aktuella versionen är 2. |
| tidsrymd |”Tick” per sekund i videon. |
| Offset |Tidsförskjutningen för tidsstämplar i ”tick”. I version 1.0 av API: er för Video att detta alltid vara 0. I framtiden scenarier som vi stöder det här värdet kan ändras. |
| ramhastighet |Bildrutor per sekund av videon. |
| Bredd, höjd |Refererar till bredden och höjden av video i bildpunkter. |
| Start |Starta tidsstämpeln i ”tick”. |
| Varaktighet |Längden på händelsen i ”tick”. |
| Intervall |Intervall för varje post i händelsen i ”tick”. |
| Händelser |Varje händelse fragment innehåller rörelse identifieras inom den varaktigheten. |
| Typ |I den aktuella versionen är det alltid '2' för allmän rörelse. Den här etiketten ger Video-API: er möjlighet att kategorisera rörelser i framtida versioner. |
| RegionID |Detta kommer alltid att 0 i den här versionen som beskrivs ovan. Den här etiketten ger Video API möjlighet att hitta rörelse i olika områden i framtida versioner. |
| Regioner |Refererar till området i videon där du är intresserad rörelse. <br/><br/>-”id” representerar region – i den här versionen finns bara en, ID 0. <br/>-”typ” representerar formen för den region som intresserar dig för rörelse. För närvarande stöds ”rektangel” och ”polygon”.<br/> Om du har angett ”rektangel”, har regionen dimensioner i X, Y, bredd och höjd. X och Y-koordinaterna representerar övre vänstra Xyserver koordinaterna för region i intervallet 0,0 till 1,0 normaliserade skalan. Visar storleken på regionen i en normaliserade skala med 0,0 till 1,0 bredd och höjd. I den aktuella versionen korrigeras alltid X, Y, bredd och höjd på 0, 0 och 1, 1. <br/>Om du har angett ”polygon”, har regionen dimensioner i punkter. <br/> |
| fragment |Metadata är chunked upp till olika segment som kallas fragment. Varje avsnitt innehåller en start, varaktighet, antalet och händelse(r). Ett fragment med inga händelser innebär att inga rörelse identifierades under den starttid och varaktighet. |
| Hakparenteserna] |Varje hakparentes representerar ett intervall i händelsen. Tomma parenteser för intervallet innebär att inga rörelse upptäcktes. |
| Platser |Den nya posten under händelser visar platsen där rörelse inträffade. Det här är mer specifik än identifiera zoner. |

JSON-exemplet nedan visar utdata:

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
* Inkommande video format som stöds omfattar MP4, MOV och WMV.
* Rörelseidentifiering är optimerad för stilla bakgrund videor. Algoritmen fokuserar på att minska falsklarm, till exempel ljusförändringar och skuggor.
* Vissa rörelse kanske inte identifieras på grund av tekniska utmaningar; natt vision videor, delvis transparenta objekt och små objekt.

## <a name="net-sample-code"></a>Exempelkod för .NET

Följande program visar hur du:

1. Skapa en tillgång och överför en mediefil till tillgången.
2. Skapa ett jobb med en aktivitet för identifiering av video rörelse baserat på en konfigurationsfil som innehåller följande json-förinställda: 
   
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

3. Hämta JSON utdatafilerna. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 

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
            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);

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
[Azure Media Services rörelsedetektor blogg](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services Analytics-översikt](media-services-analytics-overview.md)

[Azure Media Analytics demonstrationer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

