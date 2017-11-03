---
title: "Avancerade encoding med Media Encoder Premium arbetsflödet | Microsoft Docs"
description: "Lär dig mer om att koda med Media Encoder Premium arbetsflöde. Kodexemplen är skrivna i C# och använder Media Services SDK för .NET."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 0f4c87ac-810a-4d42-8df8-923dff2016c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.openlocfilehash: 2b03853bf07e05c07fd730d5e8a8563963887921
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Avancerade encoding med Media Encoder Premium arbetsflöde
> [!NOTE]
> Media Encoder Premium arbetsflöde medieprocessor som beskrivs i det här avsnittet är inte tillgänglig i Kina.
>
>

E-mepd på Microsoft.com för premium-kodare frågor.

## <a name="overview"></a>Översikt
Microsoft Azure Media Services presenterar den **Media Encoder Premium arbetsflöde** medieprocessor. Detta processor erbjudanden förskott kodning funktioner för arbetsflöden din premium på begäran.

Följande avsnitt beskriver information som rör **Media Encoder Premium arbetsflöde**:

* [Formaterar stöds av Media Encoder Premium arbetsflödet](media-services-premium-workflow-encoder-formats.md) – Discusses filen formaterar och codec-rutiner som stöds av **Media Encoder Premium arbetsflöde**.
* [Översikt över och jämförelse av Azure på begäran mediet kodare](media-services-encode-asset.md) jämför kodning funktionerna i **Media Encoder Premium arbetsflöde** och **Media Encoder Standard**.

Det här avsnittet visar hur du kodar med **Media Encoder Premium arbetsflöde** med hjälp av .NET.

Kodning uppgifter för den **Media Encoder Premium arbetsflöde** kräver en separat konfigurationsfil, kallas för ett arbetsflödesfil. Filerna har filnamnstillägget .workflow och skapas med hjälp av den [Arbetsflödesdesignern](media-services-workflow-designer.md) verktyget.

Du kan också få standard Arbetsflödesfiler [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Mappen innehåller också en beskrivning av dessa filer.

Arbetsflödesfiler måste överföras till Media Services-kontot som en tillgång och tillgången ska skickas till aktiviteten kodning.

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Kodning exempel

I följande exempel visar hur du kodar med **Media Encoder Premium arbetsflöde**.

Utför följande steg:

1. Skapa en tillgång och överför en arbetsflödesfil.
2. Skapa en tillgång och överför en mediefil källa.
3. Hämta media-processorn ”Media Encoder Premium arbetsflöde”.
4. Skapa ett jobb och en aktivitet.

    I de flesta fall konfigurationssträngen för aktiviteten är tom (som i följande exempel). Vissa avancerade scenarier (som kräver att du att ange egenskaper för runtime dynamiskt) i så fall skulle du ange en XML-sträng kodning aktiviteten. Exempel på sådana scenarier är: skapa en överlägget, parallella och sekventiella media att gå till, textning.
5. Lägga till två inkommande tillgångar i aktiviteten.

    1. 1 – arbetsflöde tillgången.
    2. 2 – video tillgången.

    >[!NOTE]
    >Arbetsflödet tillgången måste läggas till aktivitet innan media tillgången.
   Konfigurationssträngen för den här uppgiften ska vara tomt.
   
6. Skicka kodningsjobbet.

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
                    ConfigurationManager.AppSettings["AADTenantDomain"];
                private static readonly string _RESTAPIEndpoint =
                    ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

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
                    var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
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

E-mepd på Microsoft.com för premium-kodare frågor.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
