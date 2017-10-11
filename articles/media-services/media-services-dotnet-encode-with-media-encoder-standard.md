---
title: "Koda en tillgång med Media Encoder Standard med hjälp av .NET | Microsoft Docs"
description: "Det här avsnittet visar hur du använder .NET för att koda en tillgång med Media Encoder Strandard."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 929592368501c54277748bf46b2160c9058db3fb
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Koda en tillgång med Media Encoder Standard med hjälp av .NET
Kodningsjobb är en av de vanligaste bearbetningsuppgifterna i Media Services. Du skapar kodningsjobb för att konvertera mediefiler från en kodning till en annan. När du kodar kan du använda Media Services-inbyggda Media-kodaren. Du kan också använda en kodare som tillhandahålls av Media Services partner; kodare från tredje part är tillgängliga via Azure Marketplace. 

Det här avsnittet visar hur du använder .NET för att koda dina tillgångar med Media Encoder Standard (MES). Media Encoder Standard konfigureras med hjälp av en kodare förinställningar beskrivs [här](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Det rekommenderas att du alltid koda källfilerna till en MP4-uppsättningen med anpassad bithastighet och sedan konvertera uppsättningen till önskade format med hjälp av den [dynamisk paketering](media-services-dynamic-packaging-overview.md). 

Om din utdatatillgången är lagringskrypterad, måste du konfigurera principen för tillgångsleverans. Mer information finns i [konfigurera tillgångsleveransprincip](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES producerar en utdatafil med ett namn som innehåller den första 32 tecknen i namnet indatafilen. Namnet är baserat på vad som anges i filen förinställda. Till exempel ”FileName”: ”{Basename} _ {Index} {tillägg}”. {Basename} ersätts av de första 32 tecknen i namnet på filen med indata.
> 
> 

### <a name="mes-formats"></a>MES format
[Format och codec](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES-förinställningar
Media Encoder Standard konfigureras med hjälp av en kodare förinställningar beskrivs [här](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Indata och utdata metadata
När du kodar en inkommande tillgång (eller tillgångar) med hjälp av MES du hämta ett utdatatillgången vid den lyckas slutförandet av denna koda aktivitet. Utdatatillgången innehåller video, ljud, miniatyrer, manifestet, etc. som du använder för kodning.

Utdatatillgången innehåller också en fil med metadata om inkommande tillgången. Namnet på XML-metadatafilen har följande format: < asset_id > _metadata.xml (till exempel 41114ad3-eb5e - 4c 57 8d 92-5354e2b7d4a4_metadata.xml), där < asset_id > är AssetId värdet av inkommande tillgången. Schemat för den här inkommande XML-metadata beskrivs [här](media-services-input-metadata-schema.md).

Utdatatillgången innehåller också en fil med metadata om utdatatillgången. Namnet på XML-metadatafilen har följande format: < source_file_name > _manifest.xml (till exempel BigBuckBunny_manifest.xml). Schemat för dessa utdata metadata XML beskrivs [här](media-services-output-metadata-schema.md).

Om du vill undersöka antingen två metadatafiler du skapar en SAS-positionerare och hämta filen till den lokala datorn. Du hittar ett exempel på hur du skapar en SAS-positionerare och hämta en fil med Media Services .NET SDK-tilläggen.

## <a name="download-sample"></a>Hämta exempel
Du kan hämta och kör ett exempel som visar hur du kodar med MES från [här](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Exempelkod för .NET

Följande kodexempel använder Media Services .NET SDK för att utföra följande uppgifter:

* Skapa ett kodningsjobb.
* Hämta en referens till Media Encoder Standard-kodare.
* Ange om du vill använda den [anpassningsbar strömning](media-services-autogen-bitrate-ladder-with-mes.md) förinställda. 
* Lägga till en enskild kodning uppgift i jobbet. 
* Ange indata tillgången ska kodas.
* Skapa en utdata tillgång som innehåller den kodade tillgången.
* Lägga till en händelsehanterare för att kontrollera jobbförloppet.
* Skicka jobbet.

#### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exempel 

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;

        namespace MediaEncoderStandardSample
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

                static void Main(string[] args)
                {
                    var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
                    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();

                    // Encode and generate the output using the "Adaptive Streaming" preset.
                    EncodeToAdaptiveBitrateMP4Set(asset);

                    Console.ReadLine();
                }

                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    // Create a task with the encoding details, using a string preset.
                    // In this case "Adaptive Streaming" preset is used.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "Adaptive Streaming",
                        TaskOptions.None);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }

                private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
[Så här genererar du miniatyrbild med Media Encoder Standard med .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Media Services Encoding: översikt](media-services-encode-asset.md)

