---
title: Redigera ansikten med Azure Media Analytics | Microsoft Docs
description: Det här avsnittet visar hur du redigera ansikten med Azure media analytics.
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
ms.date: 02/09/2019
ms.author: juliako;
ms.openlocfilehash: 7fa1bec1b6f83b18684f6139bf6ac8db1ae9daec
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004901"
---
# <a name="redact-faces-with-azure-media-analytics-legacy"></a>Redigera ansikten med Azure Media Analytics (äldre)
## <a name="overview"></a>Översikt
**Azure Media Redactor** är en [Azure Media Analytics](media-services-analytics-overview.md) mediebearbetare (MP) som erbjuder skalbara ansiktsredigering i molnet. Ansiktsredigering kan du ändra din video för att kunna oskärpa ansikten för valda individer. Du kanske vill använda tjänsten ansiktsredigering i offentliga säkerhet samt nyhetsmediescenarier. Några minuter med material som innehåller flera ansikten kan ta timmar att redigera manuellt, men med den här tjänsten ansikte ansiktsredigering processen tar bara några få enkla steg. Mer information finns i [detta](https://azure.microsoft.com/blog/azure-media-redactor/) blogg.

Den här artikeln innehåller information om **Azure Media Redactor** och visar hur du använder det med Media Services SDK för .NET.

## <a name="face-redaction-modes"></a>Ansikte ansiktsredigering lägen
Ansiktsigenkänning ansiktsredigering fungerar genom att identifiera ansikten i varje bildruta från video och spåra ansikte objektet båda framåt och bakåt i tiden, så att samma person kan oskarpa från andra vinklar samt. Processen för automatisk ansiktsredigering är komplex och har inte alltid skapa 100% av önskad utdata, därför Media Analytics ger dig med ett par olika sätt att ändra det slutgiltiga resultatet.

Förutom ett helt automatisk läge finns det ett arbetsflöde för två gånger, vilket gör val/programvara-selection av hittades ansikten via en lista med ID: N. Även använder om du vill göra godtyckliga per justering av ramens MP du en metadatafil i JSON-format. Det här arbetsflödet är uppdelat i **analysera** och **Redact** lägen. Du kan kombinera de två lägena i ett enda steg som körs av aktiviteterna i ett jobb. Det här läget kallas **kombineras**.

### <a name="combined-mode"></a>Kombinerade läge
Detta ger en omarbetade mp4 automatiskt utan att alla manuella som indata.

| Fas | Filnamn | Anteckningar |
| --- | --- | --- |
| Indatatillgången |foo.bar |Video i WMV, MOV eller MP4-format |
| Ange config |Jobbkonfigurationen förinställning |{'version':'1.0 ', 'alternativ ”: {'mode': 'kombineras'}} |
| Utdatatillgången |foo_redacted.mp4 |Video med suddiga tillämpas |

#### <a name="input-example"></a>Inkommande exempel:
[se den här videon](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Exempel på utdata:
[se den här videon](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analysera läge
Den **analysera** pass två gånger arbetsflödets tar en video som indata och producerar en JSON-fil med ansikts-platser och jpg-bilder för var och en identifieras ansikte.

| Fas | Filnamn | Anteckningar |
| --- | --- | --- |
| Indatatillgången |foo.bar |Video i WMV, MPV eller MP4-format |
| Ange config |Jobbkonfigurationen förinställning |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Utdatatillgången |foo_annotations.json |Anteckningens data av ansikte platser i JSON-format. Detta kan redigeras av användaren för att ändra suddar ut avgränsar rutorna. Se exemplet nedan. |
| Utdatatillgången |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Beskurna jpg för var och en har identifierats ansikte, där siffran indikerar labelId av de står inför |

#### <a name="output-example"></a>Exempel på utdata:

```json
    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated
```

### <a name="redact-mode"></a>Redigera läge
Andra beräkningen av arbetsflödet tar ett större antal indata som måste kombineras till ett enskilt objekt.

Detta omfattar en lista med ID: N till oskärpa, den ursprungliga videon och anteckningar JSON. Det här läget används anteckningarna för att ge suddar ut på indatavideo.

Utdata från analysera passet innehåller inte den ursprungliga videon. Videon måste laddas upp till indatatillgången för aktiviteten Redact läge och valt som den primära filen.

| Fas | Filnamn | Anteckningar |
| --- | --- | --- |
| Indatatillgången |foo.bar |Video i WMV, MPV eller MP4-format. Samma video som i steg 1. |
| Indatatillgången |foo_annotations.json |anteckningar metadatafilen från den första fasen, med valfria ändringar. |
| Indatatillgången |foo_IDList.txt (valfritt) |Valfritt radmatning avgränsade lista med face ID: N att redigera. Om inget anges används det alla gör ansikten suddiga. |
| Ange config |Jobbkonfigurationen förinställning |{'version':'1.0 ', 'alternativ ”: {” läge ”:” redigera ”}} |
| Utdatatillgången |foo_redacted.mp4 |Video med suddiga tillämpas baserat på kommentarer |

#### <a name="example-output"></a>Exempel på utdata
Det här är utdata från en IDList med ett ID som har valts.

[se den här videon](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Exempel foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Oskärpa typer

I den **kombineras** eller **Redact** läge, 5 olika oskärpa lägen som du kan välja bland via den inkommande JSON-konfigurationen: **Låg**, **Med**, **hög**, **Box**, och **svart**. Som standard **Med** används.

Du hittar exempel på vilka oskärpa nedan.

### <a name="example-json"></a>Exempel på JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Låg

![Låg](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Hög

![Hög](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Svart

![Svart](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Element i JSON-filen för utdata

Ansiktsredigering MP ger hög identifiering och spårning som kan identifiera upp till 64 mänskliga ansikten i en video ram. Främre ansikten ger det bästa resultatet, samtidigt som sida ansikten och små ansikten (mindre än eller lika med 24 x 24 bildpunkter) är svårt.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET-exempelkod

Följande program visar hur du:

1. Skapa en tillgång och överför en mediefil till tillgången.
2. Skapa ett jobb med en aktivitet för redigering av ansikte baserat på en konfigurationsfil som innehåller följande json-förinställningen: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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

namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Medieanalys-demonstrationer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

