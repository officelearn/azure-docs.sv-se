---
title: Digitalisera text med Azure Media Analytics OCR | Microsoft Docs
description: Azure Media Analytics OCR (optisk teckenläsning) kan du konvertera textinnehåll i videofiler till redigerbara och sökbara digitala text.  På så sätt kan du automatisera extrahering av användbara metadata från video signalen medieinnehåll.
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
ms.openlocfilehash: 91fad34073d7505c596bedfb6c93946ee7393dd7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825616"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Använda Azure Media Analytics för att konvertera textinnehåll i videofiler till digital text  
## <a name="overview"></a>Översikt
Om du vill extrahera textinnehåll från dina videofiler och generera en redigerbara och sökbara digitala text, bör du använda Azure Media Analytics OCR (optisk teckenläsning). Den här Azure-Mediebearbetare identifierar textinnehåll i dina videofiler och genererar textfiler för din användning. OCR kan du automatisera extrahering av användbara metadata från video signalen medieinnehåll.

När den används tillsammans med en sökmotor, kan du enkelt indexera ditt medieinnehåll med text och förbättra det lättare att hitta ditt innehåll. Detta är mycket användbart i mycket text videon som en videoinspelning eller skärmdump av ett bildspel. Mediebearbetare för Azure OCR är optimerat för digital text.

Den **Azure Media OCR** medieprocessor förhandsvisas just nu.

Den här artikeln innehåller information om **Azure Media OCR** och visar hur du använder det med Media Services SDK för .NET. Mer information och exempel finns i [den här bloggen](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>OCR indatafiler
Videofiler. För närvarande stöds följande format: MP4 MOV och WMV.

## <a name="task-configuration"></a>Uppgiftskonfiguration
Uppgiftskonfiguration (förinställning). När du skapar en uppgift med **Azure Media OCR**, måste du ange en förinställning med hjälp av JSON eller XML-konfiguration. 

>[!NOTE]
>OCR-motorn tar bara en bild-region med minst 40 bildpunkter att maximal 32000 bildpunkter som en giltig inmatning i båda höjd och bredd.
>

### <a name="attribute-descriptions"></a>Attributbeskrivningar
| Attributnamn | Beskrivning |
| --- | --- |
|AdvancedOutput| Om du ställer in AdvancedOutput till true, innehåller JSON-utdata platsdata för varje enstaka ord (utöver fraser och regioner). Om du inte vill se den här informationen kan du ställa in flaggan till false. Standardvärdet är FALSKT. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Språk |(valfritt) beskriver språket i texten som ska eftersökas. Något av följande: Identifiera automatiskt (standard), arabiska, ChineseSimplified, ChineseTraditional, tjeckiska, danska, nederländska, engelska, finska, franska, tyska, grekiska, ungerska, italienska, japanska, koreanska, norska, polska, portugisiska, rumänska, ryska, SerbianCyrillic, SerbianLatin , Slovakiska, spanska, svenska och turkiska. |
| TextOrientation |(valfritt) beskriver orientering text som ska eftersökas.  ”Left” innebär att upp alla bokstäver är pekar mot vänster.  Standardtext (t.ex. den som finns i en bok) kan anropas ”dig” objektorienterad.  Något av följande: Identifiera automatiskt (standard), upp, höger nedåt, till vänster. |
| TimeInterval |(valfritt) beskriver samplingsfrekvensen.  Standardvärdet är varje sekund 1/2.<br/>JSON-format –: mm: ss. SSS (standard 00:00:00.500)<br/>XML-format – W3C XSD varaktighet i enhetshanteringen (standard PT0.5) |
| DetectRegions |(valfritt) En matris med DetectRegion objekt att ange regioner inom ramen video att identifiera text.<br/>Ett DetectRegion-objekt består av följande fyra heltalsvärden:<br/>Vänster – bildpunkter från vänstermarginal<br/>Överst – bildpunkter från den övre marginalen<br/>Bredd – bredden för regionen i bildpunkter<br/>Höjd – höjden på regionen i bildpunkter |

#### <a name="json-preset-example"></a>Förinställda JSON-exempel

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

#### <a name="xml-preset-example"></a>Förinställda XML-exempel

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

## <a name="ocr-output-files"></a>OCR utdatafiler
Utdata från mediebearbetare OCR är en JSON-fil.

### <a name="elements-of-the-output-json-file"></a>Element i JSON-filen för utdata
Video OCR-utdata innehåller tid-segmenterade data om de tecken som finns i videon.  Du kan använda attribut, till exempel språk eller orientering hone in på exakt de ord som du är intresserad av att analysera. 

Utdata innehåller följande attribut:

| Element | Beskrivning |
| --- | --- |
| Tidsskala |”ticken” per sekund av videon |
| Offset |tider för tidsstämplar. Det kommer alltid ske 0 i version 1.0 av Video-API: er. |
| Bildfrekvens |Bildrutor per sekund av videon |
| Bredd |bredd i bildpunkter |
| Höjd |höjden på videon i bildpunkter |
| Fragment |matris med tidsbaserade segment av videon där metadata chunked |
| start |Starttid för ett fragment i ”ticken” |
| varaktighet |längden på ett fragment i ”ticken” |
| interval |intervall för varje händelse inom det angivna fragmentet |
| evenemang |matris som innehåller regioner |
| region |objekt som representerar identifierade ord eller fraser |
| language |språket i texten som upptäcks inom en region |
| Orientering |orientering textens identifieras inom en region |
| rader |matris med rader med text som har identifierats inom en region |
| text |texten som |

### <a name="json-output-example"></a>Exempel för JSON-utdata
I följande exempel på utdata innehåller allmänna videoinformation och flera video fragment. Den innehåller varje region som identifieras av OCR MP med språket och dess textorientering i varje video fragment. Regionen innehåller också varje ord rad i den här regionen med den Radtext, radens position och varje ord information (word innehåll, position och förtroende) i den här raden. Följande är ett exempel och jag placera vissa infogade kommentarer.

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

## <a name="net-sample-code"></a>.NET-exempelkod

Följande program visar hur du:

1. Skapa en tillgång och överför en mediefil till tillgången.
2. Skapa ett jobb med en konfiguration/förinställning OCR-fil.
3. Hämta JSON utdatafilerna. 
   
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
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

