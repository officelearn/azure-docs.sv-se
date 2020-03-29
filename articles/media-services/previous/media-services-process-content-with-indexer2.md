---
title: Indexera mediefiler med förhandsversionen av Azure Media Indexer 2 | Microsoft-dokument
description: Med Azure Media Indexer kan du göra innehållet i dina mediefiler sökbart och generera en fulltextavskrift för dold textning och nyckelord. Det här avsnittet visar hur du använder förhandsgranskning av Media Indexer 2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: adsolank
ms.openlocfilehash: c24218dc116803ca0e0a1f166b7b54b24fc4d5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163802"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexera mediefiler med förhandsversionen av Azure Media Indexer 2

> [!NOTE]
> **Azure Media Indexer 2-medieprocessorn** dras tillbaka. För pensioneringsdatumen finns i det här [äldre komponentavsnittet.](legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ersätter den äldre medieprocessorn. Mer information finns i [Migrera från Azure Media Indexer och Azure Media Indexer 2 till Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

**Med Azure Media Indexer 2 Preview media** processor (MP) kan du göra mediefiler och innehåll sökbart, samt generera spårar för dold textning. Jämfört med den tidigare versionen av [Azure Media Indexer](media-services-index-content.md)utför **Azure Media Indexer 2 Preview** snabbare indexering och erbjuder bredare språkstöd. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (mandarin, förenklad), portugisiska, arabiska, ryska och japanska.

Förhandsversionen av **Azure Media Indexer 2** är för närvarande i förhandsversion.

Den här artikeln visar hur du skapar indexeringsjobb med **förhandsversionen av Azure Media Indexer 2**.

## <a name="considerations"></a>Överväganden

Följande gäller:
 
* Indexer 2 stöds inte i Azure China 21Vianet och Azure Government.
* När du indexerar innehåll ska du se till att använda mediefiler som har mycket tydligt tal (utan bakgrundsmusik, brus, effekter eller mikrofonbrus). Några exempel på lämpligt innehåll är: inspelade möten, föreläsningar eller presentationer. Följande innehåll kanske inte är lämpligt för indexering: filmer, TV-program, allt med blandade ljud- och ljudeffekter, dåligt inspelat innehåll med bakgrundsljud (väs).
 
## <a name="input-and-output-files"></a>Indata- och utdatafiler
### <a name="input-files"></a>Indatafiler
Ljud- eller videofiler

### <a name="output-files"></a>Utdatafiler
Ett indexeringsjobb kan generera filer med dold textning i följande format:  

* **TTML (TTML)**
* **WebVTT (olika)**

CC-filer (Closed Caption) i dessa format kan användas för att göra ljud- och videofiler tillgängliga för personer med nedsatt hörsel.

## <a name="task-configuration-preset"></a>Aktivitetskonfiguration (förinställd)
När du skapar en indexeringsuppgift med **förhandsversionen av Azure Media Indexer 2**måste du ange en konfigurationsförinställning.

Följande JSON anger tillgängliga parametrar.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>Språk som stöds
Förhandsversionen av Azure Media Indexer 2 stöder tal-till-text för följande språk (när du anger språknamnet i aktivitetskonfigurationen använder du 4-teckenskod inom parentes enligt nedan):

* Engelska [EnUs]
* Spanska [EsEs]
* Kinesiska (mandarin, förenklad) [ZhCn]
* Franska [FrFr]
* Tyska [DeDe]
* Italienska [ItIt]
* Portugisiska [PtBr]
* Arabiska (egyptiska) [ArEg]
* Japanska [JaJp]
* Ryska [RuRu]
* Brittisk engelska [EnGb]
* Spanska (Mexiko) [EsMx] 

## <a name="supported-file-types"></a>Filtyper som stöds

Information om filtyper som stöds finns i avsnittet [codec/format som stöds.](media-services-media-encoder-standard-formats.md#input-containerfile-formats)

## <a name="net-sample-code"></a>EXEMPELkod för .NET

Följande program visar hur du:

1. Skapa en tillgång och ladda upp en mediefil till tillgången.
2. Skapa ett jobb med en indexeringsuppgift baserat på en konfigurationsfil som innehåller följande json-förinställning:

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. Ladda ner utdatafilerna. 
   
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

namespace IndexContent
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

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

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

[Demos för Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

