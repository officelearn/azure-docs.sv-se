---
title: Redigera ansikten med Azure Media Analytics | Microsoft-dokument
description: Azure Media Redactor är en Azure Media Analytics-medieprocessor som erbjuder skalbar ansiktsredigering i molnet. Den här artikeln visar hur du redigerar ansikten med Azure-medieanalys.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6a1b7a76ef1efda51f09ac733b3d434235ff40ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900305"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Redigera ansikten med Azure Media Analytics 
## <a name="overview"></a>Översikt
**Azure Media Redactor** är en Azure Media Analytics-medieprocessor (MP) som erbjuder skalbar ansiktsredigering i molnet. [Azure Media Analytics](media-services-analytics-overview.md) Med ansiktsutredigering kan du ändra videon för att göra vissa personer oskarpa. Du kanske vill använda ansiktsutredigeringstjänsten i scenarier för allmän säkerhet och nyhetsmedier. Några minuter av bilder som innehåller flera ansikten kan ta timmar att redigera manuellt, men med den här tjänsten ansikte bortredigeringsverktyg processen kommer att kräva bara några enkla steg. Mer information finns i [den här](https://azure.microsoft.com/blog/azure-media-redactor/) bloggen.

Den här artikeln innehåller information om **Azure Media Redactor** och visar hur du använder den med Media Services SDK för .NET.

## <a name="face-redaction-modes"></a>Lägen för ansiktsredigering
Ansiktsigenkänning fungerar genom att upptäcka ansikten i varje bildruta av video och spåra ansiktsobjektet både framåt och bakåt i tiden, så att samma person kan suddas ut från andra vinklar också. Den automatiserade bortredigeringsprocessen är komplex och producerar inte alltid 100% av önskad utdata, av denna anledning ger Media Analytics dig ett par sätt att ändra den slutliga produktionen.

Förutom ett helautomatiskt läge finns det ett tvåkortsarbetsflöde, som gör det möjligt att välja/avvälska ut hittade ansikten via en lista över ID:er. För att göra godtyckliga justeringar per ram använder MP också en metadatafil i JSON-format. Det här arbetsflödet är uppdelat i **analys-** och **redactlägen.** Du kan kombinera de två lägena i ett enda pass som kör båda aktiviteterna i ett jobb. det här läget kallas **Kombinerat**.

### <a name="combined-mode"></a>Kombinerat läge
Detta ger en redigerad mp4 automatiskt utan manuell ingång.

| Fas | Filnamn | Anteckningar |
| --- | --- | --- |
| Ingående tillgång |foo.bar (på/på)foo.bar |Video i WMV-, MOV- eller MP4-format |
| Konfiguration av ingång |Förinställning för jobbkonfiguration |{'version':'1.0', 'options': {'mode':'combined'}} |
| Utgående tillgång |foo_redacted.mp4 |Video med oskärpa tillämpas |

#### <a name="input-example"></a>Exempel på indata:
[visa den här videon](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Utdataexempel:
[visa den här videon](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analysera läge
**Analyspasset** för arbetsflödet med två pass tar en videoinmatning och producerar en JSON-fil med ansiktsplatser och jpg-bilder av varje upptäckt ansikte.

| Fas | Filnamn | Anteckningar |
| --- | --- | --- |
| Ingående tillgång |foo.bar (på/på)foo.bar |Video i WMV-, MPV- eller MP4-format |
| Konfiguration av ingång |Förinställning för jobbkonfiguration |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Utgående tillgång |foo_annotations.json |Anteckningsdata för ansiktsplatser i JSON-format. Detta kan redigeras av användaren för att ändra de suddiga markeringsramarna. Se exempel nedan. |
| Utgående tillgång |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |En beskuren jpg av varje upptäckt ansikte, där numret anger etikettenId av ansiktet |

#### <a name="output-example"></a>Utdataexempel:

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
Det andra passet i arbetsflödet tar ett större antal indata som måste kombineras till en enda tillgång.

Detta inkluderar en lista över ID:er som ska suddas ut, den ursprungliga videon och anteckningarna JSON. I det här läget används anteckningarna för att oskärpa på indatavideon.

Utdata från Analyze-passet innehåller inte den ursprungliga videon. Videon måste överföras till indatatillgången för redact-lägesuppgiften och väljas som primär fil.

| Fas | Filnamn | Anteckningar |
| --- | --- | --- |
| Ingående tillgång |foo.bar (på/på)foo.bar |Video i WMV-, MPV- eller MP4-format. Samma video som i steg 1. |
| Ingående tillgång |foo_annotations.json |anteckningar metadatafil från fas ett, med valfria ändringar. |
| Ingående tillgång |foo_IDList.txt (valfritt) |Valfri ny radavgränsad lista över ansikts-ID:er som ska redigeras. Om den lämnas tom suddar det ut alla ansikten. |
| Konfiguration av ingång |Förinställning för jobbkonfiguration |{'version':'1.0', 'options': {'mode':'redact'}} |
| Utgående tillgång |foo_redacted.mp4 |Video med oskärpa som tillämpas baserat på anteckningar |

#### <a name="example-output"></a>Exempel på utdata
Detta är utdata från en IDList med ett ID valt.

[visa den här videon](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Exempel foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Oskärpa typer

I **läget Kombinerad** eller **Redigerad** finns det 5 olika oskärpalägen du kan välja mellan via JSON-inmatningskonfigurationen: **Låg**, **Med**, **Hög**, **Box**och **Svart**. Som standard **med** används.

Du kan hitta exempel på oskärpa typer nedan.

### <a name="example-json"></a>Exempel JSON:

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

## <a name="elements-of-the-output-json-file"></a>Delar av utdata-JSON-filen

Den Redaction MP ger hög precision ansikte plats upptäckt och spårning som kan upptäcka upp till 64 mänskliga ansikten i en videoram. Frontas ansikten ger bäst resultat, medan sidoytor och små ytor (mindre än eller lika med 24x24 pixlar) är utmanande.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>EXEMPELkod för .NET

Följande program visar hur du:

1. Skapa en tillgång och ladda upp en mediefil till tillgången.
2. Skapa ett jobb med en ansiktsåtgärdsuppgift baserat på en konfigurationsfil som innehåller följande json-förinställning: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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
[Översikt över Azure Media Services Analytics](media-services-analytics-overview.md)

[Demos för Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

