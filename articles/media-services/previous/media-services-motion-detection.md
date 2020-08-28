---
title: Identifiera rörelser med Azure-medieanalys | Microsoft Docs
description: Med hjälp av MP (Azure Media Motion Detector Media processor) kan du på ett effektivt sätt identifiera intresse områden inom en i övrigt lång och en video som inte är i händelse av händelse.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 17b325ab15f55a8fc02729c6f5ed0cb0330ecca2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016843"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Identifiera rörelser med Azure-medieanalys

> [!NOTE]
> **Azure Media motion Detector** medie processorn kommer att dras tillbaka. Information om återställnings datumet finns i avsnittet [äldre komponenter](legacy-components.md) .
 
## <a name="overview"></a>Översikt

Med hjälp av MP ( **Azure Media motion Detector** Media processor) kan du på ett effektivt sätt identifiera intresse områden inom en i övrigt lång och en video som inte är i händelse av händelse. Rörelse identifiering kan användas på statiska kamera tagningar för att identifiera delar av videon där rörelsen sker. Den genererar en JSON-fil som innehåller en metadata med tidsstämplar och den bindnings region där händelsen inträffade.

Den här tekniken är riktad mot säkerhets video flöden och kan kategorisera rörelsen i relevanta händelser och falska positiva identifieringar, till exempel skuggor och belysnings ändringar. På så sätt kan du generera säkerhets aviseringar från kamera flöden utan att skicka skräp post med oändliga irrelevanta händelser, samtidigt som du kan extrahera en mängd intresse från långa övervaknings videor.

**Azure Media motion Detector** MP är för närvarande en för hands version.

Den här artikeln innehåller information om  **Azure Media motion Detector** och visar hur du använder det med Media Services SDK för .net

## <a name="motion-detector-input-files"></a>Indatafiler för rörelse detektor
Videofiler. För närvarande stöds följande format: MP4, MOV och WMV.

## <a name="task-configuration-preset"></a>Uppgifts konfiguration (förval)
När du skapar en aktivitet med **Azure Media motion Detector**måste du ange en konfigurations för inställning. 

### <a name="parameters"></a>Parametrar
Du kan använda följande parametrar:

| Namn | Alternativ | Beskrivning | Default |
| --- | --- | --- | --- |
| sensitivityLevel |Sträng: låg, medium, hög |Anger känslighets nivån då rörelserna rapporteras. Justera detta för att justera antalet falska positiva identifieringar. |säker |
| frameSamplingValue |Positivt heltal |Anger med vilken frekvens algoritmen körs. 1 lika med varje bild ruta, 2 betyder varje andra ram och så vidare. |1 |
| detectLightChange |Boolean: true, false |Anger om ljus ändringar rapporteras i resultaten |! |
| mergeTimeThreshold |XS-Time: hh: mm: SS<br/>Exempel: 00:00:03 |Anger tidsfönstret mellan rörelse händelser där 2 händelser kombineras och rapporteras som 1. |00:00:00 |
| detectionZones |En matris med identifierings zoner:<br/>– Detektions zon är en matris med 3 eller fler punkter<br/>-Point är en x-och y-koordinat från 0 till 1. |Beskriver listan över de avstånd för polygon som ska användas.<br/>Resultat rapporteras med zonerna som ett ID, med det första som "ID": 0 |En zon som täcker hela ramen. |

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

## <a name="motion-detector-output-files"></a>Utdatafiler för rörelse detektor
Ett rörelse identifierings jobb returnerar en JSON-fil i utmatnings till gången, som beskriver rörelse aviseringarna och deras kategorier i videon. Filen innehåller information om tid och varaktighet för den rörelse som identifierats i videon.

API: et för motion-igenkänning innehåller indikatorer när det finns objekt i rörelse i en fast bakgrunds video (till exempel en övervaknings video). Rörelse detektorn tränas för att minska falska larm, till exempel belysnings-och skugg ändringar. Aktuella begränsningar för algoritmerna är exempel på natt syn-videor, halv genomskinliga objekt och små objekt.

### <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>Element i JSON-filen för utdata
> [!NOTE]
> I den senaste versionen har JSON-formatet för utdata ändrats och kan utgöra en brytande ändring för vissa kunder.
> 
> 

I följande tabell beskrivs elementen i JSON-filen för utdata.

| Element | Beskrivning |
| --- | --- |
| version |Detta avser versionen av video-API: et. Den aktuella versionen är 2. |
| tidsplan |"Tickar" per sekund för videon. |
| offset |Tids förskjutningen för tidsstämplar i "ticker". I version 1,0 av video-API: er, är det alltid 0. I framtida scenarier som vi stöder kan detta värde ändras. |
| ram |Bildrutor per sekund i videon. |
| bredd, höjd |Avser videons bredd och höjd i bild punkter. |
| start |Start-tidsstämpeln i "ticks". |
| varaktighet |Händelsens längd i "ticks". |
| interval |Intervallet för varje post i händelsen, i "Tick". |
| händelser |Varje event-fragment innehåller den rörelse som identifierats inom den tids perioden. |
| typ |I den aktuella versionen är det alltid "2" för allmän rörelse. Den här etiketten ger video-API: er möjlighet att kategorisera rörelsen i framtida versioner. |
| regionId |Som förklaras ovan kommer detta alltid att vara 0 i den här versionen. Den här etiketten ger video-API flexibiliteten att hitta rörelse i olika regioner i framtida versioner. |
| områdena |Avser det områden i videon där du bryr dig om rörelse. <br/><br/>-"ID" representerar region området – i den här versionen finns det bara ett, ID 0. <br/>-"typ" representerar formen på den region du bryr dig om för rörelse. För närvarande stöds "rektangel" och "polygon".<br/> Om du har angett "rektangel" har regionen dimensioner i X, Y, width och height. X-och Y-koordinaterna representerar de övre vänstra XY-koordinaterna för regionen i en normaliserad skala på 0,0 till 1,0. Bredden och höjden representerar storleken på regionen i en normaliserad skala på 0,0 till 1,0. I den aktuella versionen är X, Y, width och height alltid fast som 0, 0 och 1. <br/>Om du har angett "polygon" har regionen dimensioner i punkter. <br/> |
| fragment |Metadata delas upp i olika segment som kallas fragment. Varje fragment innehåller en start, varaktighet, intervallnummer och händelser. Ett fragment utan händelser innebär att ingen rörelse upptäcktes under den Start tiden och varaktigheten. |
| hakparenteser [] |Varje klammer representerar ett intervall i händelsen. Tomma hakparenteser för det intervallet innebär att ingen rörelse har identifierats. |
| platser |I den här nya posten under händelser visas platsen där rörelsen ägde rum. Detta är mer exakt än identifierings zonerna. |

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
* De inspelnings video format som stöds är MP4, MOV och WMV.
* Rörelse identifiering är optimerad för videor med Station ära bakgrunder. Algoritmen fokuserar på att minska falska larm, till exempel belysnings ändringar och skuggor.
* En del rörelse kanske inte upptäcks på grund av tekniska utmaningar. till exempel videor med natt sikt, halv genomskinliga objekt och små objekt.

## <a name="net-sample-code"></a>.NET-exempel kod

Följande program visar hur du:

1. Skapa en till gång och överför en mediefil till till gången.
2. Skapa ett jobb med en identifierings uppgift för video rörelser baserat på en konfigurations fil som innehåller följande JSON-förval: 
   
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
[Azure Media Services rörelse detektor blogg](https://azure.microsoft.com/blog/motion-detector-update/)

[Översikt över Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure-medieanalys demonstrationer](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

