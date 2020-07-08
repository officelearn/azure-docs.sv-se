---
title: Indexera mediefiler med Azure Media Indexer 2 för hands version | Microsoft Docs
description: Azure Media Indexer gör det möjligt för dig att söka efter innehållet i dina mediefiler och generera en full text avskrift för dold textning och nyckelord. Det här avsnittet visar hur du använder Media Indexer 2 Preview.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78163802"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indexera mediefiler med Azure Media Indexer 2 för hands version

> [!NOTE]
> **Azure Media Indexer 2** medie processorn kommer att dras tillbaka. Se det här avsnittet om [äldre komponenter](legacy-components.md) för datum för indragningen. [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ersätter denna äldre medie processor. Mer information finns i [Migrera från Azure Media Indexer och Azure Media Indexer 2 till Azure Media Services video Indexer](migrate-indexer-v1-v2.md).

Med den **Azure Media Indexer 2-förhands gransknings** medie processorn (MP) kan du göra mediefiler och innehåll sökbara, samt generera dold textning spår. Jämfört med den tidigare versionen av [Azure Media Indexer](media-services-index-content.md)utför **Azure Media Indexer 2 för hands** versionen snabbare indexering och erbjuder bredare språk stöd. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (mandariner, förenklad), portugisiska, arabiska, ryska och japanska.

**Azure Media Indexer 2 för hands versions** hantering är för närvarande en för hands version.

Den här artikeln visar hur du skapar indexerings jobb med **Azure Media Indexer 2 Preview**.

## <a name="considerations"></a>Att tänka på

Följande gäller:
 
* Indexerare 2 stöds inte i Azure Kina 21Vianet och Azure Government.
* När du indexerar innehåll måste du se till att använda mediefiler som har mycket tydliga tal (utan Bakgrunds musik, brus, effekter eller mikrofon hiss). Några exempel på lämpligt innehåll är: registrerade möten, föreläsningar eller presentationer. Följande innehåll kanske inte är lämpligt för indexering: filmer, TV-program, allt med blandad ljud-och ljud effekter, dåligt inspelat innehåll med bakgrunds brus (hiss).
 
## <a name="input-and-output-files"></a>In-och utdatafiler
### <a name="input-files"></a>Indatafiler
Ljud-eller videofiler

### <a name="output-files"></a>Utdatafiler
Ett indexerings jobb kan skapa filer med dold textning i följande format:  

* **TTML**
* **WebVTT**

Filer med dold textning (CC) i dessa format kan användas för att göra ljud-och videofiler tillgängliga för personer med hörsel funktions hinder.

## <a name="task-configuration-preset"></a>Uppgifts konfiguration (förval)
När du skapar en indexerings uppgift med **Azure Media Indexer 2 för hands version**måste du ange en konfigurations för inställning.

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
Azure Media Indexer 2-förhands granskning har stöd för tal till text för följande språk (när du anger språk namnet i aktivitets konfigurationen använder du koden med fyra tecken inom hakparenteser som du ser nedan):

* Engelska [EnUs]
* Spanska [Spanien]
* Kinesiska (mandariner, förenklad) [ZhCn]
* Franska [Frankrike]
* Tyska [DeDe]
* Italienska [ItIt]
* Portugisiska [Brasilien]
* Arabiska (egyptiska) [ArEg]
* Japanska [JaJp]
* Ryska [RuRu]
* Brittisk engelska [EnGb]
* Spanska (Mexiko) [EsMx] 

## <a name="supported-file-types"></a>Filtyper som stöds

Information om vilka filtyper som stöds finns i avsnittet [codec-filer/format som stöds](media-services-media-encoder-standard-formats.md#input-containerfile-formats) .

## <a name="net-sample-code"></a>.NET-exempel kod

Följande program visar hur du:

1. Skapa en till gång och överför en mediefil till till gången.
2. Skapa ett jobb med en indexerings uppgift baserat på en konfigurations fil som innehåller följande JSON-förval:

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

[Azure-medieanalys demonstrationer](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

