---
title: Identifiera ansikts-och känslo med Azure-medieanalys | Microsoft Docs
description: Det här avsnittet visar hur du identifierar ansikten och känslor med Azure-medieanalys.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: b8f100d253dc5ecb321934eb49a1aba60595a5e8
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232584"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Identifiera ansikts-och känslo med Azure-medieanalys

> [!NOTE]
> **Azure Media Face Detector** medie processorn kommer att dras tillbaka. Information om återställnings datumet finns i avsnittet [äldre komponenter](legacy-components.md) .

## <a name="overview"></a>Översikt

Med MP ( **Azure Media Face Detector** Media processor) kan du räkna, spåra förflyttningar och till och med mäta åhörarnas medverkan och reaktion via ansikts uttryck. Den här tjänsten innehåller två funktioner: 

* **Ansiktsspårning**
  
    Ansikts igenkänning hittar och spårar mänskliga ansikten i en video. Du kan identifiera flera ansikten och sedan spåras de när de flyttas runt, med tids-och platsens metadata som returneras i en JSON-fil. Under spårningen försöker den ge ett konsekvent ID till samma ansikte medan personen rör sig på skärmen, även om de är blockerade eller om de inte lämnar ramen.
  
  > [!NOTE]
  > Den här tjänsten utför inte ansikts igenkänning. En person som lämnar ramen eller som kan förhindras för länge får ett nytt ID när de returneras.
  > 
  > 
* **Känslo-identifiering**
  
    Känslo-identifiering är en valfri komponent i Ansiktsigenkänning medie processorn som returnerar analyser på flera känslomässig-attribut från de ansikten som identifierats, inklusive glädje, ledsenhet, frukt, ilska och mycket annat. 

**Azure Media Face Detector** MP är för närvarande en för hands version.

Den här artikeln innehåller information om **Azure Media Face Detector** och visar hur du använder det med Media Services SDK för .net.

## <a name="face-detector-input-files"></a>Indatafiler för ansikts detektor
Videofiler. För närvarande stöds följande format: MP4, MOV och WMV.

## <a name="face-detector-output-files"></a>Utdatafiler för ansikts igenkänning
API för ansikts identifiering och spårning tillhandahåller identifiering och spårning av topp precisions platser som kan identifiera upp till 64 mänskliga ansikten i en video. Front ansikten ger bäst resultat, medan sido ytor och små ytor (mindre än eller lika med 24x24 bild punkter) kanske inte är lika EXAKTA.

Identifierade och spårade ansikten returneras med koordinater (vänster, topp, bredd och höjd) som anger platsen för ansikten i bild punkter, samt ett ansikts-ID-nummer som visar spårning av den enskilda. Ansikts-ID-nummer är känsliga att återställa under förhållanden när front ytan tappas bort eller överlappar varandra i ramen, vilket leder till att vissa personer får flera ID: n.

## <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Element i JSON-filen för utdata

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Ansikts detektor använder metoder för fragmentering (där metadata kan delas upp i tidsbaserade segment och du kan bara hämta det du behöver) och segmentering (där händelserna är uppdelade om de blir för stora). Några enkla beräkningar kan hjälpa dig att transformera data. Till exempel, om en händelse som startades vid 6300 (Tick), med en tids skala på 2997 (Tick/s) och RAM hastighet på 29,97 (bild rutor/sekund), sedan:

* Start/tidsskala = 2,1 sekunder
* Sekunder x RAM hastighet = 63 bild rutor

## <a name="face-detection-input-and-output-example"></a>Exempel på indata och utdata för ansikts igenkänning
### <a name="input-video"></a>Inmatad video
[Inmatad video](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Uppgifts konfiguration (förval)
När du skapar en aktivitet med **Azure Media Face Detector**måste du ange en konfigurations för inställning. Följande konfigurations förval är bara för ansikts igenkänning.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Beskrivningar av attribut
| Attributnamn | Beskrivning |
| --- | --- |
| Läge |Snabb och snabbare bearbetnings hastighet, men är mindre exakt (standard).|

### <a name="json-output"></a>JSON-utdata
Följande exempel på JSON-utdata trunkerades.

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>Exempel på indata och utdata för känslo-identifiering
### <a name="input-video"></a>Inmatad video
[Inmatad video](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Uppgifts konfiguration (förval)
När du skapar en aktivitet med **Azure Media Face Detector**måste du ange en konfigurations för inställning. Följande konfigurations för inställning anger att JSON ska skapas baserat på känslo identifiering.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Beskrivningar av attribut
| Attributnamn | Beskrivning |
| --- | --- |
| Läge |Ansikten: endast ansikts igenkänning.<br/>PerFaceEmotion: returnera känslo oberoende för varje ansikts igenkänning.<br/>AggregateEmotion: returnera de genomsnittliga känslo-värdena för alla ansikten i ramen. |
| AggregateEmotionWindowMs |Använd IF AggregateEmotion mode (Välj). Anger längden på videon som används för att producera varje sammanlagt resultat, i millisekunder. |
| AggregateEmotionIntervalMs |Använd IF AggregateEmotion mode (Välj). Anger i vilken frekvens som sammanställnings resultaten ska skapas. |

#### <a name="aggregate-defaults"></a>Aggregera standardvärden
Nedan visas rekommenderade värden för mängd fönster och intervall inställningar. AggregateEmotionWindowMs får inte vara längre än AggregateEmotionIntervalMs.

|| Standardvärden (s) | Max (s) | Min (s) |
|--- | --- | --- | --- |
| **AggregateEmotionWindowMs** |0,5 |2 |0,25|
| **AggregateEmotionIntervalMs** |0,5 |1 |0,25|

### <a name="json-output"></a>JSON-utdata
JSON-utdata för sammanställd känslo (trunkerad):

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>Begränsningar
* De inspelnings video format som stöds är MP4, MOV och WMV.
* Det identifierbara storleks intervallet för ansikts är 24x24 till 2048x2048 bild punkter. Ytorna utanför det här intervallet kommer inte att identifieras.
* För varje video är det maximala antalet ansikten som returneras 64.
* Vissa ansikten kanske inte kan identifieras på grund av tekniska utmaningar. till exempel mycket stora ansikts vinklar (Head-attityd) och stora ocklusion. Front-och närbelägna ansikten har bäst resultat.

## <a name="net-sample-code"></a>.NET-exempel kod

Följande program visar hur du:

1. Skapa en till gång och överför en mediefil till till gången.
2. Skapa ett jobb med en ansikts identifierings uppgift baserat på en konfigurations fil som innehåller följande JSON-förval: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Hämta JSON-filerna för utdata. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklings miljön och fyll i app.config-filen med anslutnings information, enligt beskrivningen i [Media Services utveckling med .net](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exempel

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace FaceDetection
{
    class Program
    {
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

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

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
[Översikt över Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure-medieanalys demonstrationer](https://amslabs.azurewebsites.net/demos/Analytics.html)

