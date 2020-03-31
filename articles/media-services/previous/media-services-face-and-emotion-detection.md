---
title: Identifiera ansikte och känslor med Azure Media Analytics | Microsoft-dokument
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
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: 2d746167f993438e5fce467365844df2078c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919319"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Identifiera ansikte och känslor med Azure Media Analytics

> [!NOTE]
> **Medieprocessorn för Azure Media Face Detector** dras tillbaka. För återgivningsdatumet finns i avsnittet [äldre komponenter.](legacy-components.md)

## <a name="overview"></a>Översikt

**Med Azure Media Face Detector-medieprocessorn** (MP) kan du räkna, spåra rörelser och till och med mäta publikens deltagande och reaktion via ansiktsuttryck. Den här tjänsten innehåller två funktioner: 

* **Ansiktsigenkänning**
  
    Ansiktsigenkänning hittar och spårar mänskliga ansikten i en video. Flera ansikten kan identifieras och sedan spåras när de flyttas runt, med tid och plats metadata returneras i en JSON-fil. Under spårning, försöker det att ge ett konsekvent ID till samma ansikte medan personen flyttar runt på skärmen, även om de är blockerade eller kort lämna ramen.
  
  > [!NOTE]
  > Denna tjänst utför inte ansiktsigenkänning. En person som lämnar ramen eller blir blockerad för länge kommer att få ett nytt ID när de återvänder.
  > 
  > 
* **Emotion upptäckt**
  
    Emotion Detection är en valfri komponent i Face Detection Media Processor som returnerar analys på flera känslomässiga attribut från ansikten upptäckt, inklusive lycka, sorg, rädsla, ilska och mycket mer. 

**Azure Media Face Detector** MP är för närvarande i förhandsversion.

Den här artikeln innehåller information om **Azure Media Face Detector** och visar hur du använder den med Media Services SDK för .NET.

## <a name="face-detector-input-files"></a>Indatafiler för ansiktsdetektor
Videofiler. För närvarande stöds följande format: MP4, MOV och WMV.

## <a name="face-detector-output-files"></a>Utdatafiler för ansiktsdetektor
Api:et för ansiktsigenkänning och spårning ger identifiering och spårning av hög precision som kan identifiera upp till 64 personer i en video. Frontas ansikten ger bäst resultat, medan sidoytor och små ytor (mindre än eller lika med 24x24 pixlar) kanske inte är lika exakta.

De identifierade och spårade ytorna returneras med koordinater (vänster, överkant, bredd och höjd) som anger platsen för ansikten i bilden i pixlar, samt ett ansikts-ID-nummer som anger spårning av den personen. Face ID-nummer är benägna att återställa under omständigheter när frontytan försvinner eller överlappar i ramen, vilket resulterar i att vissa individer tilldelas flera ID:n.

## <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Delar av utdata-JSON-filen

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Face Detector använder tekniker för fragmentering (där metadata kan brytas upp i tidsbaserade bitar och du kan ladda ner bara vad du behöver), och segmentering (där händelserna bryts upp om de blir för stora). Några enkla beräkningar kan hjälpa dig att transformera data. Om till exempel en händelse startade vid 6300 (fästingar), med en tidsskala på 2997 (fästingar/sek) och bildrutehastighet på 29,97 (bildrutor/sek), så:

* Start/tidsskala = 2,1 sekunder
* Sekunder x Ramhastighet = 63 bildrutor

## <a name="face-detection-input-and-output-example"></a>Exempel på ansiktsigenkänning och utdata
### <a name="input-video"></a>Inmatningsvideo
[Indatavideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Aktivitetskonfiguration (förinställd)
När du skapar en uppgift med **Azure Media Face Detector**måste du ange en konfigurationsförinställning. Följande konfigurationsförinställning är bara för ansiktsigenkänning.

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
| Läge |Snabb - snabb bearbetningshastighet, men mindre exakt (standard).|

### <a name="json-output"></a>JSON-utgång
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


## <a name="emotion-detection-input-and-output-example"></a>Exempel på indata för emotionering och utdata
### <a name="input-video"></a>Inmatningsvideo
[Indatavideo](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Aktivitetskonfiguration (förinställd)
När du skapar en uppgift med **Azure Media Face Detector**måste du ange en konfigurationsförinställning. Följande konfigurationsförinställning anger att JSON ska skapas baserat på känsloidentifiering.

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
| Läge |Ansikten: Endast ansiktsigenkänning.<br/>PerFaceEmotion: Returnera känslor oberoende av varandra för varje ansiktsigenkänning.<br/>AggregateEmotion: Returnera genomsnittliga känslovärden för alla ansikten i bildrutan. |
| AggregateEmotionWindowMs |Använd om AggregateEmotion-läge är markerat. Anger längden på den video som används för att producera varje aggregerat resultat, i millisekunder. |
| AggregateEmotionIntervalmerna |Använd om AggregateEmotion-läge är markerat. Anger med vilken frekvens som ska ge aggregerade resultat. |

#### <a name="aggregate-defaults"></a>Aggregerade standardvärden
Nedan finns rekommenderade värden för det sammanlagda fönstret och intervallinställningarna. AggregateEmotionWindowMs bör vara längre än AggregateEmotionIntervalMs.

|| Standardvärden | Max(er) | Min(s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0.25|
| AggregateEmotionIntervalmerna |0,5 |1 |0.25|

### <a name="json-output"></a>JSON-utgång
JSON utgång för aggregerade känslor (trunkeras):

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
* De indatavideoformat som stöds inkluderar MP4, MOV och WMV.
* Detekterbara ansiktsstorleksområdet är 24x24 till 2048x2048 pixlar. Ansiktena utanför det här intervallet kommer inte att identifieras.
* För varje video är det maximala antalet ansikten som returneras 64.
* Vissa ansikten kanske inte upptäcks på grund av tekniska utmaningar. till exempel mycket stora ansiktsvinklar (huvud-pose), och stor ocklusion. Främre och nära frontala ansikten har de bästa resultaten.

## <a name="net-sample-code"></a>EXEMPELkod för .NET

Följande program visar hur du:

1. Skapa en tillgång och ladda upp en mediefil till tillgången.
2. Skapa ett jobb med en ansiktsidentifieringsuppgift baserat på en konfigurationsfil som innehåller följande json-förinställning: 

    ```json
            {
                "version": "1.0"
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

[Demos för Azure Media Analytics](https://amslabs.azurewebsites.net/demos/Analytics.html)

