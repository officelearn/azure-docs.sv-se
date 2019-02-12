---
title: Identifiera Ansikts- och Känslo med Azure Media Analytics | Microsoft Docs
description: Det här avsnittet visar hur du identifierar ansikten och känslor med Azure Media Analytics.
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
ms.date: 02/10/2019
ms.author: milanga;juliako;
ms.openlocfilehash: fa51290623f68e94db9cb1b28cd69c88b5d5cf18
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000081"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Identifiera Ansikts- och Känslo med Azure Media Analytics
## <a name="overview"></a>Översikt
Den **Ansiktsigenkänning i Azure Media** mediebearbetare (MP) kan du räkna, spåra förflyttningar och även för mätare målgrupp deltagande och reaktion via ansiktsuttryck. Den här tjänsten innehåller två funktioner: 

* **Ansiktsigenkänning**
  
    Ansiktsigenkänning söker efter och spårar ansikten i en video. Flera ansikten kan identifieras och spåras senare när de flyttas runt, med tid och plats metadata som returneras i en JSON-fil. Under spårning försöker ge en konsekvent ID till samma sida när personen som flyttas på skärmen, även om de hindras eller kort lämna ramen.
  
  > [!NOTE]
  > Ansiktsigenkänning utför inte i den här tjänsten. En person som lämnar ramen eller blir skymd för länge får ett nytt ID när de kommer tillbaka.
  > 
  > 
* **Känsloigenkänning**
  
    Känsloigenkänning är en valfri komponent i ansikte identifiering Mediebearbetare som returnerar analysis på flera känslomässig attribut från de ansikten har identifierats, inklusive lycka, sorg, rädsla, ilska, med mera. 

Den **Ansiktsigenkänning i Azure Media** MP förhandsvisas just nu.

Den här artikeln innehåller information om **Ansiktsigenkänning i Azure Media** och visar hur du använder det med Media Services SDK för .NET.

## <a name="face-detector-input-files"></a>Står inför detektor indatafiler
Videofiler. För närvarande stöds följande format: MP4 MOV och WMV.

## <a name="face-detector-output-files"></a>Står inför detektor utdatafiler
Ansikts-API för identifiering och spårning av ger hög identifiering och spårning av som kan identifiera upp till 64 mänskliga ansikten i en video. Främre ansikten ger det bästa resultatet, när sida ansikten och små ansikten (mindre än eller lika med 24 x 24 bildpunkter) kanske inte är så korrekt.

Identifierade och spårade ansikten returneras med koordinaterna (vänster, top, bredd och höjd) som anger platsen för ansikten i bild i bildpunkter, samt ett face ID-nummer som indikerar spårningen av den individen. Face ID-nummer är lätt att återställa under omständigheter när främre ansiktet förloras eller överlappas i RAM, vilket resulterar i vissa personer komma tilldelats flera ID: N.

## <a id="output_elements"></a>Element i JSON-filen för utdata

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Ansiktsigenkänning använder tekniker för fragmentering (där metadata kan delas upp i tidsbaserade segment och du kan ladda ned det du behöver) och segmentering (där händelserna delas om de blir för stor). Några enkla beräkningar kan hjälpa dig att transformera data. Exempel: om en händelse startade 6300 (ticks), med en tid för 2997 (ticken per sekund) och bildhastighet 29,97 (bildrutor per sekund), sedan:

* Start/tidsskala = 2,1 sekunder
* Sekunder x Framerate = 63 bildrutor

## <a name="face-detection-input-and-output-example"></a>Står inför identifiering av indata och utdata exempel
### <a name="input-video"></a>Indatavideon
[Indatavideon](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Uppgiftskonfiguration (förinställning)
När du skapar en uppgift med **Ansiktsigenkänning i Azure Media**, måste du ange en förinställning för konfigurationen. Följande configuration förinställningen är bara för ansiktsigenkänning.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Attributbeskrivningar
| Attributnamn | Beskrivning |
| --- | --- |
| Läge |Fast - snabb bearbetning av hastighet, men mindre exakta (standard).|

### <a name="json-output"></a>JSON-utdata
Följande exempel visar JSON-utdata har trunkerats.

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


## <a name="emotion-detection-input-and-output-example"></a>Känsloigenkänning indata och utdata exempel
### <a name="input-video"></a>Indatavideon
[Indatavideon](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Uppgiftskonfiguration (förinställning)
När du skapar en uppgift med **Ansiktsigenkänning i Azure Media**, måste du ange en förinställning för konfigurationen. Följande configuration förinställningen anger för att skapa JSON-baserade på känsloigenkänning.

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


#### <a name="attribute-descriptions"></a>Attributbeskrivningar
| Attributnamn | Beskrivning |
| --- | --- |
| Läge |Ansikten: Endast ansiktsspårning.<br/>PerFaceEmotion: Returnera känslor separat för varje ansiktsigenkänning.<br/>AggregateEmotion: Returnera känslo-genomsnittliga värden för alla ansikten i ram. |
| AggregateEmotionWindowMs |Använd om AggregateEmotion läge har valt. Anger hur lång video som användes för att skapa varje aggregerade resultat, i millisekunder. |
| AggregateEmotionIntervalMs |Använd om AggregateEmotion läge har valt. Anger hur ofta att skapa mängdresultat. |

#### <a name="aggregate-defaults"></a>Sammanställd standardvärden
Nedan är rekommenderade värden för de sammanställda fönster och intervall. AggregateEmotionWindowMs bör vara längre än AggregateEmotionIntervalMs.

|| Standardvärden (s) | Max(s) | Minuten(minuterna) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0.25|
| AggregateEmotionIntervalMs |0,5 |1 |0.25|

### <a name="json-output"></a>JSON-utdata
JSON-utdata för sammanställd känslor (trunkerad):

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
* De video indataformat som stöds är MP4, MOV och WMV.
* Ansikts-flashminnet storleksintervall är 24 x 24 till 2048 x 2048 bildpunkter. Kommer inte att identifiera ansikten utanför det här intervallet.
* För varje video är det maximala antalet ansikten returnerade 64.
* Vissa ansikten kanske inte identifieras på grund av tekniska utmaningar; till exempel mycket stora ansikte vinklar (head-attityd) och stora ocklusion. Främre och nästan direkt ansikten har bästa resultat.

## <a name="net-sample-code"></a>.NET-exempelkod

Följande program visar hur du:

1. Skapa en tillgång och överför en mediefil till tillgången.
2. Skapa ett jobb med en aktivitet för identifiering av ansikte baserat på en konfigurationsfil som innehåller följande json-förinställningen: 

    ```json
            {
                "version": "1.0"
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
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Medieanalys-demonstrationer](http://amslabs.azurewebsites.net/demos/Analytics.html)

