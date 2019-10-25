---
title: Digitalisera text med Azure-medieanalys OCR | Microsoft Docs
description: Azure-medieanalys OCR (optisk tecken läsning) gör att du kan konvertera text innehåll i videofiler till redigerbar, sökbar digital text.  På så sätt kan du automatisera extraheringen av meningsfulla metadata från video signalen på mediet.
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
ms.openlocfilehash: 48b5136505c3d0cb5e2e2027f832655e4b3445bf
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881731"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Använd Azure-medieanalys för att konvertera text innehåll i video filer till digital text  

> [!IMPORTANT]
> Granska [indragnings planer](media-services-analytics-overview.md#retirement-plans) för vissa medie processorer.

## <a name="overview"></a>Översikt
Om du behöver Extrahera text innehåll från dina videofiler och generera en redigerbar, sökbar digital text, bör du använda Azure-medieanalys OCR (optisk tecken läsning). Azure Media-processorn identifierar text innehåll i dina videofiler och genererar textfiler för din användning. Med OCR kan du automatisera extraheringen av meningsfulla metadata från video signalen på mediet.

När det används tillsammans med en sökmotor kan du enkelt indexera dina media efter text och förbättra det innehåll som kan upptäckas. Detta är mycket användbart i mycket text video, t. ex. en video inspelning eller skärm bild av en bild spels presentation. Azure OCR-medie processorn är optimerad för digital text.

**Azure Media** -processorn för OCR-media är för närvarande en för hands version.

Den här artikeln innehåller information om **Azure Media OCR** och visar hur du använder den med Media Services SDK för .net. Mer information och exempel finns i [den här bloggen](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Filer med OCR-indatafiler
Videofiler. För närvarande stöds följande format: MP4, MOV och WMV.

## <a name="task-configuration"></a>Uppgiftskonfiguration
Uppgifts konfiguration (förval). När du skapar en aktivitet med **Azure Media OCR**måste du ange en konfigurations för inställning med JSON eller XML. 

>[!NOTE]
>OCR-motorn tar bara ett bild område med minst 40 bild punkter till maximalt 32000 pixlar som en giltig indata i både höjd/bredd.
>

### <a name="attribute-descriptions"></a>Beskrivningar av attribut
| Attributnamn | Beskrivning |
| --- | --- |
|AdvancedOutput| Om du anger AdvancedOutput till True innehåller JSON-utdata positions data för varje enskilt ord (förutom fraser och regioner). Om du inte vill se de här uppgifterna ställer du in flaggan på falskt. Standardvärdet är false. Mer information finns i [den här bloggen](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Språk |(valfritt) beskriver språket för den text som du vill titta på. Något av följande: identifiera (standard), arabiska, ChineseSimplified, ChineseTraditional, Tjeckiska Danska, nederländska, engelska, finska, franska, tyska, grekiska, ungerska, italienska, japanska, koreanska, norska, polska, portugisiska, rumänska, ryska, SerbianCyrillic, SerbianLatin, slovakiska, spanska, svenska och turkiska. |
| TextOrientation |(valfritt) beskriver orienteringen för den text som du vill titta på.  "Left" innebär att övre delen av alla bokstäver riktas mot vänster.  Standard texten (som du hittar i en bok) kan kallas "upp".  Något av följande: identifiera automatiskt (standard), upp, höger, nedåt och till vänster. |
| TimeInterval |(valfritt) beskriver samplings frekvensen.  Standardvärdet är var 1/2 sekund.<br/>JSON-format – HH: mm: SS. SSS (standard 00:00:00.500)<br/>XML-format – W3C XSD duration primitiv (standard PT 0,5) |
| DetectRegions |valfritt En matris med DetectRegion-objekt som anger regioner i video ramen där text ska identifieras.<br/>Ett DetectRegion-objekt består av följande fyra heltals värden:<br/>Vänster – pixlar från vänster marginal<br/>Överkant – pixlar från topp marginalen<br/>Bredd – bredden på regionen i bild punkter<br/>Höjd – höjden på regionen i bild punkter |

#### <a name="json-preset-example"></a>Exempel på JSON-förval

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

## <a name="ocr-output-files"></a>Filer för OCR-utdata
Utdata från OCR Media processor är en JSON-fil.

### <a name="elements-of-the-output-json-file"></a>Element i JSON-filen för utdata
Videons OCR-utdata innehåller tidssegmenterade data på de tecken som finns i videon.  Du kan använda attribut som språk eller orientering för att finslipa på exakt de ord som du är intresse rad av att analysera. 

Utdata innehåller följande attribut:

| Element | Beskrivning |
| --- | --- |
| Tidsskala |"Tick" per sekund för videon |
| Offset |tids förskjutning för tidsstämplar. I version 1,0 av video-API: er, är det alltid 0. |
| Bildfrekvens |Bild rutor per sekund för videon |
| LED |videoklippets bredd i bild punkter |
| Våghöjd |videoklippets höjd i bild punkter |
| Fragment |matris med tidsbaserade segment med video som metadata segmenteras i |
| start |Start tid för ett fragment i "ticks" |
| Giltighet |längden på ett fragment i "tickas" |
| interval |intervall för varje händelse inom det aktuella fragmentet |
| evenemang |matris som innehåller regioner |
| regionen |objekt som representerar identifierade ord eller fraser |
| language |språk för texten som identifieras inom en region |
| orientering |orientering för text som identifieras inom en region |
| operationsföljdsrader |matris med rader med text som har identifierats inom en region |
| text |den faktiska texten |

### <a name="json-output-example"></a>Exempel på JSON-utdata
Följande utdata-exempel innehåller allmän video information och flera videofragment. I varje video fragment innehåller den alla regioner som identifieras av OCR MP med språket och dess text orientering. Regionen innehåller också varje ord rad i den här regionen med radens text, radens position och alla ord information (Word-innehåll, position och förtroende) på den här raden. Följande är ett exempel och jag infogar kommentarer infogade.

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

## <a name="net-sample-code"></a>.NET-exempel kod

Följande program visar hur du:

1. Skapa en till gång och överför en mediefil till till gången.
2. Skapa ett jobb med en OCR-konfiguration/för inställnings fil.
3. Hämta JSON-filerna för utdata. 
   
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

## <a name="related-links"></a>Tillhörande länkar
[Översikt över Azure Media Services Analytics](media-services-analytics-overview.md)

