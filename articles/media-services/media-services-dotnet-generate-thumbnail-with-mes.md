---
title: "Så här genererar du miniatyrer med Media Encoder Standard med .NET"
description: "Det här avsnittet visar hur du använder .NET för att koda en tillgång och generera miniatyrbilder samtidigt med Media Encoder Standard."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 02bbeafd9cd8ca93f22cf9e1a2c107e01c082ba3
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Så här genererar du miniatyrer med Media Encoder Standard med .NET

Du kan använda Media Encoder Standard för att generera en eller flera miniatyrer från din videoinmatning i [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics), eller [BMP](https://en.wikipedia.org/wiki/BMP_file_format) filer. Du kan skicka uppgifter som genererar endast bilder eller kan du kombinera miniatyr generation kodning. Den här artikeln innehåller några exempel XML och JSON miniatyr förinställningar för dessa scenarier. I slutet av artikeln finns en [exempelkoden](#code_sample) som visar hur du använder Media Services .NET SDK för att utföra aktiviteten kodning.

Mer information om de element som används i exemplet förinställningar bör du granska [Media Encoder Standard schemat](media-services-mes-schema.md).

Se till att granska den [överväganden](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) avsnitt.
    
## <a name="example-of-a-single-png-file-preset"></a>Exempel på en förinställning ”PNG-fil”

Följande JSON- och XML förinställningen kan användas för att skapa en enda utflöde PNG-fil från några sekunder för inmatningen video, där kodaren gör en bästa försöket att hitta en ”intressant” ram. Observera att bildmåtten utdata har ställts in till 100%, vilket innebär att de matchar de inkommande dimensionerna. Observera även hur inställningen ”Format” i ”utdata” måste stämma överens med ”PngLayers” i avsnittet ”codec”. 

### <a name="json-preset"></a>JSON förinställda

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>XML-förinställda

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Exempel på en förinställning för ”antal JPEG-bilder”

Följande JSON- och XML förinställningen kan användas för att skapa en uppsättning 10 bilder på tidsstämplar 5% 15%,..., 95% av inkommande tidslinjen där bildstorleken har angetts en kvartal som indata video.

### <a name="json-preset"></a>JSON förinställda

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-förinställda
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Exempel på en förinställning ”en bild på en specifik tidsstämpel”

Följande JSON- och XML förinställningen kan användas för att skapa en JPEG-bild på 30 sekunder markering av video indata. Denna inställning förväntar sig indata videon mer än 30 sekunder varaktighet (annars jobbet misslyckas).

### <a name="json-preset"></a>JSON förinställda

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-förinställda
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Exempel på en förinställning ”miniatyrbilder med olika upplösning”

Följande förinställda kan användas för att generera miniatyrbilder med olika upplösning i en aktivitet. I det här exemplet på positioner 5% 15%,..., 95% av inkommande tidslinjen kodaren genererar två bilder – en 100% av inkommande video upplösning och andra vid 50%.

Observera användningen av {upplösning} makro i filnamn. Anger att encoder använda bredd och höjd som du angav i avsnittet kodning förinställda när namnet på utdata-avbildningar. Detta hjälper dig också att skilja mellan olika bilder enkelt

### <a name="json-preset"></a>JSON förinställda

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-förinställda
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Exempel på genererar en miniatyrbild vid kodning

När alla ovanstående exempel har beskrivs hur du kan skicka en kodning uppgift som bara ger bild kan kan du också kombinera ljud och kodning miniatyr generation. Berätta för följande JSON- och XML förinställningen **Media Encoder Standard** att generera en miniatyrbild under kodning.

### <a id="json"></a>JSON förinställda
Mer information om schemat finns [detta](https://msdn.microsoft.com/library/mt269962.aspx) artikel.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a id="xml"></a>XML-förinställda
Mer information om schemat finns [detta](https://msdn.microsoft.com/library/mt269962.aspx) artikel.

```csharp
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a id="code_sample"></a>Koda video och generera miniatyr med .NET

Följande kodexempel använder Media Services .NET SDK för att utföra följande uppgifter:

* Skapa ett kodningsjobb.
* Hämta en referens till Media Encoder Standard-kodare.
* Läsa in förinställningen [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) eller [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) som innehåller encoding förinställt samt information som behövs för att generera miniatyrbilder. Du kan spara den [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) eller [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) i fil- och använda följande kod för att läsa in filen.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Lägga till en enskild kodning uppgift i jobbet. 
* Ange indata tillgången ska kodas.
* Skapa en utdata tillgång som innehåller den kodade tillgången.
* Lägga till en händelsehanterare för att kontrollera jobbförloppet.
* Skicka jobbet.

Finns det [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md) artikel för instruktioner om hur du ställer in din utvecklingsmiljö.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
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

        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
        Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

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

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
                    processor,
                    configuration,
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

## <a name="considerations"></a>Överväganden
Följande gäller:

* Användningen av explicita tidsstämplar för Startintervall-steg förutsätter att Indatakällan är minst 1 minut.
* BmpImage-jpg/Png-element har Start, steg, och intervallet strängattribut – dessa kan tolkas som:
  
  * RAM-numret om de är icke-negativa heltal, till exempel ”Start”: ”120”
  * Relativt källa varaktighet om uttryckt som %-suffixet, till exempel ”Start”: ”15%”, eller
  * Tidsstämpel om uttryckt: mm: ss... format. Till exempel ”Start” ”: 00: 01:00”
    
    Du kan blanda och matcha här ska du ange.
    
    Dessutom Start har också stöd för ett särskilt makro: {bästa}, som försöker fastställa den första ”intressanta” bildrutan anteckningens innehåll: (steg och intervallet ignoreras när Start är inställd på {bäst})
  * Standardvärden: Starta: {bästa}
* Utdataformat måste tillhandahållas explicit för varje bildformat: Jpg/Png/BmpFormat. När matchar MES JpgVideo till JpgFormat och så vidare. OutputFormat introducerar ett nytt specifika bilden codec makro: {Index}, vilket måste vara finns (en gång och bara en gång) för bildformat för utdata.

## <a name="next-steps"></a>Nästa steg

Du kan kontrollera den [jobb pågår](media-services-check-job-progress.md) medan kodningsjobbet väntar.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Media Services Encoding översikt](media-services-encode-asset.md)

