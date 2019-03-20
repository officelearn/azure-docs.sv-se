---
title: Indexera mediefiler med Azure Media Indexer 2 Preview | Microsoft Docs
description: Azure Media Indexer kan du göra innehållet i mediefiler sökbart och att generera en fulltextavskrift för dold textning och nyckelord. Det här avsnittet visar hur du använder Media Indexer 2 Preview.
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
ms.date: 02/10/2019
ms.author: adsolank;juliako;
ms.openlocfilehash: 12d188b39375bce88d3adc244d217854c75f436d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861304"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexera mediefiler med Azure Media Indexer 2 förhandsgranskning
## <a name="overview"></a>Översikt
Den **förhandsversion av Azure Media Indexer 2** mediebearbetare (MP) kan du göra mediefiler och innehåll sökbart samt generera dold textning spår. Jämfört med den tidigare versionen av [Azure Media Indexer](media-services-index-content.md), **förhandsversion av Azure Media Indexer 2** utför snabbare indexering och erbjuder större språkstöd. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (Mandarin, förenklad), portugisiska, arabiska, ryska och japanska.

Den **förhandsversion av Azure Media Indexer 2** MP förhandsvisas just nu.

Den här artikeln visar hur du skapar indexerade jobb med **förhandsversion av Azure Media Indexer 2**.

> [!NOTE]
> Följande gäller:
> 
> Indexerare 2 stöds inte i Azure Kina och Azure Government.
> 
> Vid indexering av innehåll, se till att använda mediefiler med tydliga tal (utan bakgrundsmusik, buller, effekter eller mikrofon hiss). Några exempel på rätt innehåll är: registreras möten, Föreläsningar och presentationer. Följande innehåll är kanske inte lämpligt för indexering: filmer, TV-program, allt med blandad ljud och visuella effekter, registreras dåligt innehåll med bakgrundsljud (hiss).
> 
> 

Den här artikeln innehåller information om **förhandsversion av Azure Media Indexer 2** och visar hur du använder det med Media Services SDK för .NET

## <a name="input-and-output-files"></a>Inkommande och utgående filer
### <a name="input-files"></a>Inkommande filer
Ljud-eller video

### <a name="output-files"></a>Utdatafiler
Ett indexeringsjobb kan generera filer med dold textning i följande format:  

* **SAMI**
* **TTML**
* **WebVTT**

Stängd textning (kopia) filer i formaten som kan användas för att göra det tillgängligt för personer med funktionshinder höra ljud- och bildfiler.

## <a name="task-configuration-preset"></a>Uppgiftskonfiguration (förinställning)
Skapa en indexering när uppgiften med **förhandsversion av Azure Media Indexer 2**, måste du ange en förinställning för konfiguration.

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
Azure Media Indexer 2 Preview stöder tal till text för följande språk (när du anger språkets namn i uppgiftskonfigurationen, Använd 4-teckenkod omges av hakparenteser enligt nedan):

* Engelska [EnUs]
* Spanska [Spanien]
* Kinesiska (Mandarin, förenklad) [ZhCn]
* Franska [Frankrike]
* Tyska [DeDe]
* Italienska [ItIt]
* Portugisiska [Brasilien]
* Arabiska (egyptiskt) [ArEg]
* Japanska [JaJp]
* Ryska [RuRu]
* Brittisk engelska [EnGb]
* Spanska (Mexiko) [EsMx] 

## <a name="supported-file-types"></a>Filtyper som stöds

Information om filtyper som stöds finns i den [codec/format som stöds](media-services-media-encoder-standard-formats.md#input-containerfile-formats) avsnittet.

## <a name="net-sample-code"></a>.NET-exempelkod

Följande program visar hur du:

1. Skapa en tillgång och överför en mediefil till tillgången.
2. Skapa ett jobb med en indexering uppgift baserat på en konfigurationsfil som innehåller följande json-förinställningen:

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
    
3. Hämta utdatafilerna. 
   
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
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Medieanalys-demonstrationer](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

