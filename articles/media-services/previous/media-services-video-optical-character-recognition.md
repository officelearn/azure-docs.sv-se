---
title: Digitalisera text med Azure Media Analytics OCR | Microsoft-dokument
description: Med Azure Media Analytics OCR (optisk teckenigenkänning) kan du konvertera textinnehåll i videofiler till redigerbar, sökbar digital text.  På så sätt kan du automatisera utvinningen av meningsfulla metadata från mediets videosignal.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 11889bd6df0bcc9564c17fdaacc333df1d418660
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918350"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Använda Azure Media Analytics för att konvertera textinnehåll i videofiler till digital text  

> [!NOTE]
> Azure Media OCR-medieprocessorn dras tillbaka. **Azure Media OCR** För återgivningsdatumet finns i avsnittet [äldre komponenter.](legacy-components.md)

## <a name="overview"></a>Översikt
Om du behöver extrahera textinnehåll från dina videofiler och generera en redigerbar, sökbar digital text, bör du använda Azure Media Analytics OCR (optisk teckenigenkänning). Den här Azure Media-processorn identifierar textinnehåll i dina videofiler och genererar textfiler för din användning. OCR kan du automatisera utvinning av meningsfulla metadata från videosignalen för dina media.

När du används tillsammans med en sökmotor kan du enkelt indexera dina media efter text och förbättra upptäckbarheten av ditt innehåll. Detta är mycket användbart i mycket textvideo, som en videoinspelning eller skärmdump av ett bildspel presentation. Azure OCR-medieprocessorn är optimerad för digital text.

Azure Media OCR-medieprocessorn är för närvarande i förhandsversion. **Azure Media OCR**

Den här artikeln innehåller information om **Azure Media OCR** och visar hur du använder den med Media Services SDK för .NET. Mer information och exempel finns i [den här bloggen](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>OCR-indatafiler
Videofiler. För närvarande stöds följande format: MP4, MOV och WMV.

## <a name="task-configuration"></a>Uppgiftskonfiguration
Aktivitetskonfiguration (förinställd). När du skapar en uppgift med **Azure Media OCR**måste du ange en konfigurationsförinställning med JSON eller XML. 

>[!NOTE]
>OCR-motorn tar bara ett bildområde med minst 40 pixlar till maximalt 3 2000 pixlar som en giltig ingång i både höjd/bredd.
>

### <a name="attribute-descriptions"></a>Attributbeskrivningar
| Attributnamn | Beskrivning |
| --- | --- |
|Avanceratutflöde| Om du anger AdvancedOutput till true innehåller JSON-utdata positionsdata för varje ord (förutom fraser och regioner). Om du inte vill se dessa uppgifter anger du flaggan till false. Standardvärdet är false. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Språk |(valfritt) beskriver språket i texten som du vill titta på. Ett av följande: AutoDetect (standard), arabiska, kinesisksmmplad, kinesisktraditionell, tjeckisk danska, nederländsk, engelsk, finsk, fransk, tysk, grekisk, ungersk, italiensk, japansk, koreansk, norsk, polsk, portugisisk, rumänsk, rysk, SerbiskaCyrilliska, SerbiskaLatin, Slovakiska, Spanska, Svenska, Turkiska. |
| Textorientering |(valfritt) beskriver orienteringen av text som du vill titta på.  "Vänster" betyder att toppen av alla bokstäver är riktade åt vänster.  Standardtext (som den som finns i en bok) kan kallas "Up"-orienterad.  Ett av följande: AutoDetect (standard), Upp, Höger, Ned, Vänster. |
| TidInval |(frivillig uppgift) beskriver provtagningshastigheten.  Standard är varje 1 / 2 sekund.<br/>JSON-format – HH:mm:ss. SSS (standard 00:00:00.500)<br/>XML-format – W3C XSD-varaktighet primitiv (standard PT0.5) |
| Identifiera regioner |(valfritt) En matris med Identifiera regionobjekt som anger områden i videoramen där text ska identifieras.<br/>Ett DetectRegion-objekt består av följande fyra heltalsvärden:<br/>Vänster – pixlar från vänstermarginalen<br/>Överkant – pixlar från den övre marginalen<br/>Bredd – bredden på regionen i pixlar<br/>Höjd – regionens höjd i pixlar |

#### <a name="json-preset-example"></a>JSON förinställt exempel

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>Exempel på XML-förinställning

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>OCR-utdatafiler
Utdata från OCR-medieprocessorn är en JSON-fil.

### <a name="elements-of-the-output-json-file"></a>Delar av utdata-JSON-filen
Video OCR-utdata ger tidssegmenterade data om de tecken som finns i videon.  Du kan använda attribut som språk eller orientering för att finslipa på exakt de ord som du är intresserad av att analysera. 

Utdata innehåller följande attribut:

| Element | Beskrivning |
| --- | --- |
| Tidsskala |"fästingar" per sekund av videon |
| Offset |tidsförskjutning för tidsstämplar. I version 1.0 av Video API:er är detta alltid 0. |
| Bildfrekvens |Bildrutor per sekund av videon |
| bredd |videons bredd i bildpunkter |
| höjd |videons höjd i pixlar |
| Fragment |matris med tidsbaserade videosegment där metadata är segmenterade |
| start |starttiden för ett fragment i "fästingar" |
| varaktighet |längden på ett fragment i "fästingar" |
| interval |intervall för varje händelse inom det givna fragmentet |
| händelser |matris som innehåller regioner |
| regionen |objekt som representerar upptäckta ord eller fraser |
| language |språket i texten som har identifierats inom en region |
| Orientering |orientering av texten som har identifierats inom en region |
| Linjer |matris med textrader som har identifierats inom en region |
| text |den faktiska texten |

### <a name="json-output-example"></a>JSON-utdataexempel
Följande utdataexempel innehåller allmän videoinformation och flera videofragment. I varje videofragment innehåller den varje region, som identifieras av OCR MP med språket och dess textorientering. Regionen innehåller också varje ordrad i det här området med radens text, radens position och varje ordinformation (ordinnehåll, position och förtroende) på den här raden. Följande är ett exempel, och jag satte några kommentarer inline.

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>EXEMPELkod för .NET

Följande program visar hur du:

1. Skapa en tillgång och ladda upp en mediefil till tillgången.
2. Skapa ett jobb med en OCR-konfiguration/förinställd fil.
3. Ladda ner utdata JSON filer. 
   
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

namespace OCR
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

