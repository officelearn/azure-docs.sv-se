---
title: "Använd Azure Media Encoder Standard för att automatiskt skapa en bithastighet stege | Microsoft Docs"
description: "Det här avsnittet visar hur du använder Media Encoder Standard (MES) för att automatiskt skapa en bithastighet stege baserat på inkommande upplösning och bithastighet. Inkommande upplösning och bithastighet kommer aldrig att överskridas. Till exempel om indata är 720p på 3 Mbit/s, utdata kommer förblir 720p i bästa och startar priser som är lägre än 3 Mbit/s."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 63ed95da-1b82-44b0-b8ff-eebd535bc5c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: b5616aa9f8b15ab576d914fbae89a56f64c27f4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Använd Azure Media Encoder Standard för att generera en bithastighet stege automatiskt

## <a name="overview"></a>Översikt

Det här avsnittet visar hur du använder Media Encoder Standard (MES) för att automatiskt skapa en bithastighet stege (bithastighet upplösning par) baserat på inkommande upplösning och bithastighet. Den automatisk genererade förinställningen kan inte överstiga inkommande upplösning och bithastighet. Till exempel om indata är 720p på 3 Mbit/s, utdata kommer förblir 720p i bästa och startar priser som är lägre än 3 Mbit/s.

### <a name="encoding-for-streaming-only"></a>Kodning för strömning endast

Om din avsikt är att koda videon källa endast för strömning, du ska använda för ”anpassningsbar strömning” förinställningen när du skapar en kodning aktivitet. När du använder den **anpassningsbar strömning** förinställts MES kodaren kommer Intelligent cap stege en bithastighet. Men kommer du inte att styra kodningen kostnader, eftersom tjänsten bestämmer hur många lager om du vill använda och med vilken upplösning. Du kan se ett exempel på utdata-lager som produceras av MES på grund av encoding med den **anpassningsbar strömning** förinställningen i slutet av det här avsnittet. Är inte av överlagrad utdata tillgång innehåller MP4-filer där ljud och video.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kodning för strömning och progressiv hämtning

Om din avsikt är att koda videon källa för strömning samt att skapa MP4-filer för progressiv nedladdning, använder du bör den ”innehåll anpassningsbar flera bithastighet MP4” förinställningen när du skapar en kodning aktivitet. När du använder den **innehåll anpassningsbar flera bithastighet MP4** förinställts MES kodaren gäller samma kodning logik som ovan, men nu utdatatillgången innehåller MP4-filer där ljud och video är överlagrat. Du kan använda någon av dessa MP4-filer (till exempel högsta bithastighet version) som en fil för progressiv nedladdning.

## <a id="encoding_with_dotnet"></a>Encoding med Media Services .NET SDK

Följande kodexempel använder Media Services .NET SDK för att utföra följande uppgifter:

- Skapa ett kodningsjobb.
- Hämta en referens till Media Encoder Standard-kodare.
- Lägga till en kodning uppgift jobbet och ange om du vill använda den **anpassningsbar strömning** förinställda. 
- Skapa en utdata tillgång som innehåller den kodade tillgången.
- Lägga till en händelsehanterare för att kontrollera jobbförloppet.
- Skicka jobbet.

#### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exempel

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
        ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        // Field for service context.
        private static CloudMediaContext _context = null;

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

## <a id="output"></a>Utdata

Det här avsnittet visar tre exempel på utdata-lager som produceras av MES på grund av encoding med den **anpassningsbar strömning** förinställda. 

### <a name="example-1"></a>Exempel 1
Källa med höjd ”1080” och ramhastighet ”29.970” ger 6 video lager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exempel 2
Källa med höjd ”720” och ramhastighet ”23.970” ger 5 video lager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exempel 3
Källa med höjd ”360” och ramhastighet ”29.970” ger 3 video lager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Media Services Encoding översikt](media-services-encode-asset.md)

