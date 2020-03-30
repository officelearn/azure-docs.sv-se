---
title: Koda en tillgång med Media Encoder Standard med .NET | Microsoft-dokument
description: Den här artikeln visar hur du använder .NET för att koda en tillgång med Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 259e32d55f25c4a146b7ff358eb503763dd5fab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016591"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Koda en tillgång med Media Encoder Standard med .NET  

Kodningsjobb är en av de vanligaste bearbetningsåtgärderna i Media Services. Du skapar kodningsjobb för att konvertera mediefiler från en kodning till en annan. När du kodar kan du använda den inbyggda Media Services-kodaren. Du kan också använda en kodare som tillhandahålls av en Media Services-partner. Kodare från tredje part är tillgängliga via Azure Marketplace. 

Den här artikeln visar hur du använder .NET för att koda dina tillgångar med Media Encoder Standard (MES). Media Encoder Standard är konfigurerad med hjälp av en av kodarna förinställningar som beskrivs [här](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Vi rekommenderar att du alltid kodar källfilerna till en adaptiv bithastighet MP4-uppsättning och sedan konverterar uppsättningen till önskat format med dynamisk [förpackning](media-services-dynamic-packaging-overview.md). 

Om utdatatillgången är lagringskrypterad måste du konfigurera principen för tillgångsleverans. Mer information finns i [Konfigurera leveransprincip för tillgångar](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES skapar en utdatafil med ett namn som innehåller de första 32 tecknen i indatafilnamnet. Namnet baseras på vad som anges i den förinställda filen. Till exempel "Filnamn": "{Basename}_{Index}{Extension}". {Basename} ersätts med de första 32 tecknen i indatafilnamnet.
> 
> 

### <a name="mes-formats"></a>MES-format
[Format och codec-enheter](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES-förinställningar
Media Encoder Standard är konfigurerad med hjälp av en av kodarna förinställningar som beskrivs [här](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadata för indata och utdata
När du kodar en indatatillgång (eller tillgångar) med MES får du en utdatatillgång när kodaktiviteten har slutförts. Utdatatillgången innehåller video, ljud, miniatyrer, manifest, etc. baserat på den kodningsförinställning du använder.

Utdatatillgången innehåller också en fil med metadata om indatatillgången. Namnet på metadata XML-filen har följande format: <asset_id>_metadata.xml (till exempel 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), där <asset_id> är AssetId-värdet för indatatillgången. Schemat för den här XML-xml:et för indatametadata beskrivs [här](media-services-input-metadata-schema.md).

Utdatatillgången innehåller också en fil med metadata om utdatatillgången. Namnet på metadata-XML-filen har följande format: <source_file_name>_manifest.xml (till exempel BigBuckBunny_manifest.xml). Schemat för den här XML-xml:et för utdatametadata beskrivs [här](media-services-output-metadata-schema.md).

Om du vill undersöka någon av de två metadatafilerna kan du skapa en SAS-positionerare och hämta filen till den lokala datorn. Du hittar ett exempel på hur du skapar en SAS-positionerare och hämtar en fil med hjälp av Media Services .NET SDK-tillägg.

## <a name="download-sample"></a>Hämta exempel
Du kan få och köra ett exempel som visar hur du kodar med MES [härifrån](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>EXEMPELkod för .NET

I följande kodexempel används Media Services .NET SDK för att utföra följande uppgifter:

* Skapa ett kodningsjobb.
* Hämta en referens till Media Encoder Standard-kodaren.
* Ange att använda förinställningen [Adaptiv direktuppspelning.](media-services-autogen-bitrate-ladder-with-mes.md) 
* Lägg till en enskild kodningsaktivitet i jobbet. 
* Ange den indatatillgång som ska kodas.
* Skapa en utdatatillgång som innehåller den kodade tillgången.
* Lägg till en händelsehanterare för att kontrollera jobbförloppet.
* Skicka in jobbet.

#### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation enligt beskrivningen i [Media Services-utvecklingen med .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exempel 

```csharp
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

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

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
```

## <a name="advanced-encoding-features-to-explore"></a>Avancerade kodningsfunktioner för att utforska
* [Så här skapar du miniatyrer](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generera miniatyrer under kodning](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Beskära videor under kodning](media-services-crop-video.md)
* [Anpassa kodningsförinställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Överlägg eller vattenstämpel en video med en bild](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
[Så här skapar du miniatyrer med Media Encoder Standard med .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Media Services Encoding Overview](media-services-encode-asset.md)

