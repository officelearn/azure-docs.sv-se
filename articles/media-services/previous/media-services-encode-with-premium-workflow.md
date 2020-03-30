---
title: Avancerad kodning med Media Encoder Premium Workflow | Microsoft-dokument
description: Läs om hur du kodar med Media Encoder Premium Workflow. Kodexempel skrivs i C# och använder Media Services SDK för .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ca5de657ad45f53cff0cb01d5fe9cc412baf4533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792293"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Avancerad kodning med Media Encoder Premium Workflow
> [!NOTE]
> Media Encoder Premium Workflow media processor diskuteras i den här artikeln är inte tillgänglig i Kina.
>
>

## <a name="overview"></a>Översikt
Microsoft Azure Media Services introducerar **mediakodaren Premium Workflow.** Den här processorn erbjuder avancerade kodningsfunktioner för dina premiumarbetsflöden på begäran.

I följande avsnitt beskrivs information om **Media Encoder Premium Workflow:**

* [Format som stöds av Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md) – Beskriver de filformat och codec-enheter som stöds av **Media Encoder Premium Workflow**.
* [Översikt och jämförelse av Azure on-demand media-kodare](media-services-encode-asset.md) jämför kodningsfunktionerna i **Media Encoder Premium Workflow** and Media **Encoder Standard**.

Den här artikeln visar hur du kodar med **Media Encoder Premium Workflow** med .NET.

Kodningsuppgifter för **Media Encoder Premium Workflow** kräver en separat konfigurationsfil, en så kallad arbetsflödesfil. Dessa filer har ett .workflow-tillägg och skapas med verktyget [Arbetsflödesdesignern.](media-services-workflow-designer.md)

Du kan också hämta standardarbetsflödesfilerna [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Mappen innehåller också en beskrivning av dessa filer.

Arbetsflödesfilerna måste överföras till ditt Media Services-konto som en tillgång, och den här tillgången ska skickas in till kodningsuppgiften.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation enligt beskrivningen i [Media Services-utvecklingen med .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Kodningsexempel

I följande exempel visas hur du kodar med **Media Encoder Premium Workflow**.

Följande steg utförs:

1. Skapa en tillgång och ladda upp en arbetsflödesfil.
2. Skapa en tillgång och ladda upp en källmediefil.
3. Hämta medieprocessorn "Media Encoder Premium Workflow".
4. Skapa ett jobb och en uppgift.

    I de flesta fall är konfigurationssträngen för aktiviteten tom (som i följande exempel). Det finns några avancerade scenarier (som kräver att du anger körningsegenskaper dynamiskt) i vilket fall du skulle tillhandahålla en XML-sträng till kodningsuppgiften. Exempel på sådana scenarier är: skapa ett överlägg, parallella eller sekventiella mediesömmar, subtitling.
5. Lägg till två indataresurser i aktiviteten.

   1. 1: a – arbetsflödestillgången.
   2. 2. – videotillgången.

      >[!NOTE]
      >Arbetsflödestillgången måste läggas till i uppgiften före medietillgången.
      Konfigurationssträngen för den här aktiviteten ska vara tom.
   
6. Skicka kodningsjobbet.

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
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
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna en supportbiljett genom att navigera till [Ny supportförfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
