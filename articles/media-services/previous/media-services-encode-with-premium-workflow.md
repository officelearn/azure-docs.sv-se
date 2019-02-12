---
title: Avancerad kodning med Media Encoder Premium Workflow | Microsoft Docs
description: Lär dig mer om att koda med Media Encoder Premium Workflow. Kodexemplen är skrivna C# och använda Media Services SDK för .NET.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 7b91f63057fa8b781fdfab17e0837a9f5547c1eb
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55988042"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Avancerad kodning med Media Encoder Premium Workflow
> [!NOTE]
> Media Encoder Premium Workflow mediebearbetare som beskrivs i den här artikeln är inte tillgänglig i Kina.
>
>

Premium-kodare frågor kan e- mepd@microsoft.com.

## <a name="overview"></a>Översikt
Microsoft Azure Media Services presenterar den **Media Encoder Premium Workflow** medieprocessor. Den här processor erbjuder avancerade funktioner för dina arbetsflöden för på begäran av premium-kodning.

I följande avsnitt beskriver information som rör **Media Encoder Premium Workflow**:

* [Formaterar stöds av Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md) – Discusses filen format och codecs för stöds av **Media Encoder Premium Workflow**.
* [Översikt och jämförelse av kodare för Azure på begäran media](media-services-encode-asset.md) jämför kodningsfunktioner av **Media Encoder Premium Workflow** och **Media Encoder Standard**.

Den här artikeln visar hur du koda med **Media Encoder Premium Workflow** med hjälp av .NET.

Kodningsuppgifter för den **Media Encoder Premium Workflow** kräver en separat konfigurationsfil som kallas en arbetsflödesfil. De här filerna har filnamnstillägget .workflow och skapas med den [Arbetsflödesdesigner](media-services-workflow-designer.md) verktyget.

Du kan också få standard Arbetsflödesfiler [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Mappen innehåller också en beskrivning av dessa filer.

Arbetsflödesfiler måste överföras till Media Services-kontot som en tillgång och den här tillgången ska skickas till aktiviteten kodning.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Kodning exempel

I följande exempel visar hur du koda med **Media Encoder Premium Workflow**.

Utför följande steg:

1. Skapa en tillgång och ladda upp en arbetsflödesfil.
2. Skapa en tillgång och överför en mediefil källa.
3. Hämta mediebearbetare ”Media Encoder Premium Workflow”.
4. Skapa ett jobb och en uppgift.

    I de flesta fall konfigurationssträngen för aktiviteten är tom (som i följande exempel). Det finns vissa avancerade scenarier (som kräver att du kan ange egenskaper för runtime dynamiskt) i så fall skulle du ange en XML-sträng till kodning aktivitet. Exempel på sådana scenarier är: skapa ett överlägg, parallella eller sekventiella media häfta ihop det, textning.
5. Lägg till två inkommande tillgångar i uppgiften.

    1. 1 – arbetsflöde för tillgången.
    2. 2 – video tillgången.

    >[!NOTE]
    >Arbetsflöde för tillgången måste läggas till uppgiften innan media tillgången.
   Konfigurationssträngen för den här uppgiften ska vara tomt.
   
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

Premium-kodare frågor kan e- mepd@microsoft.com.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
