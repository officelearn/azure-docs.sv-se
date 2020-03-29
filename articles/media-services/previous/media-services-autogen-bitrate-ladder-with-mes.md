---
title: Använd Media Encoder Standard för att automatiskt generera en bithastighetsstege - Azure | Microsoft-dokument
description: Det här avsnittet visar hur du använder Media Encoder Standard (MES) för att automatiskt generera en bithastighetsstege baserat på indataupplösningen och bithastigheten.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: b7f0b77ba11a0c9c1670ec240caf45fcf61a934d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896024"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Använd Media Encoder Standard för att automatiskt generera en bithastighetsstege  

## <a name="overview"></a>Översikt

Den här artikeln visar hur du använder Media Encoder Standard (MES) för att automatiskt generera en bithastighetsstege (bithastighetspar) baserat på indataupplösningen och bithastigheten. Den automatiskt genererade förinställningen överskrider aldrig indataupplösningen och bithastigheten. Om indata till exempel är 720p vid 3 Mbit/s är utdata i bästa fall 720p och börjar med lägre hastigheter än 3 Mbit/s.

### <a name="encoding-for-streaming-only"></a>Kodning endast för direktuppspelning

Om din avsikt är att koda källvideon endast för direktuppspelning bör du använda förinställningen "Adaptiv direktuppspelning" när du skapar en kodningsuppgift. När du använder förinställningen **Adaptiv streaming** kommer MES-kodaren att på ett intelligent sätt kapa en bithastighetsstege. Du kommer dock inte att kunna styra kodningskostnaderna, eftersom tjänsten avgör hur många lager som ska användas och vid vilken upplösning. Du kan se exempel på utdatalager som produceras av MES som ett resultat av kodning med förinställningen **Adaptiv direktuppspelning** i slutet av den här artikeln. Utdatatillgången innehåller MP4-filer där ljud och video inte är interfolierade.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kodning för streaming och progressiv nedladdning

Om din avsikt är att koda källvideon för direktuppspelning samt att producera MP4-filer för progressiv nedladdning, bör du använda förinställningen "Content Adaptive Multiple Bitrate MP4" när du skapar en kodningsuppgift. När du använder **mp4-förinställningen Content Adaptive Multiple Bitrate** använder MES-kodaren samma kodningslogik som ovan, men nu innehåller utdatatillgången MP4-filer där ljud och video är interfolierade. Du kan använda en av dessa MP4-filer (till exempel den högsta bitrate-versionen) som en progressiv nedladdningsfil.

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Kodning med Media Services .NET SDK

I följande kodexempel används Media Services .NET SDK för att utföra följande uppgifter:

- Skapa ett kodningsjobb.
- Hämta en referens till Media Encoder Standard-kodaren.
- Lägg till en kodningsaktivitet i jobbet och ange att förinställningen **Adaptiv direktuppspelning** ska användas. 
- Skapa en utdatatillgång som innehåller den kodade tillgången.
- Lägg till en händelsehanterare för att kontrollera jobbförloppet.
- Skicka in jobbet.

#### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation enligt beskrivningen i [Media Services-utvecklingen med .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exempel

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
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

## <a name="output"></a><a id="output"></a>Produktionen

I det här avsnittet visas tre exempel på utdatalager som produceras av MES som ett resultat av kodning med förinställningen **Adaptiv direktuppspelning.** 

### <a name="example-1"></a>Exempel 1
Källa med höjd "1080" och framerate "29.970" producerar 6 videolager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exempel 2
Källa med höjd "720" och framerate "23.970" producerar 5 videolager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exempel 3
Källa med höjd "360" och framerate "29.970" producerar 3 videolager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över kodning av Media Services](media-services-encode-asset.md)

