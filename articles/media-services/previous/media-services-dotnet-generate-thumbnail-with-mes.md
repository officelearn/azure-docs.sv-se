---
title: Så här genererar du miniatyrer med Media Encoder Standard med .NET
description: Det här avsnittet visar hur du använder .NET för att koda en tillgång och generera miniatyrer samtidigt med Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bc29c098bcf7ef1d1a2e2532a00c95f0ec7e927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61244237"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Så här genererar du miniatyrer med Media Encoder Standard med .NET 

Du kan använda Media Encoder Standard för att generera en eller flera miniatyrer från indatavideon i [JPEG-,](https://en.wikipedia.org/wiki/JPEG) [PNG-](https://en.wikipedia.org/wiki/Portable_Network_Graphics)eller [BMP-bildfilformat.](https://en.wikipedia.org/wiki/BMP_file_format) Du kan skicka uppgifter som bara producerar bilder eller kombinera miniatyrgenerering med kodning. Den här artikeln innehåller några exempel på XML- och JSON-miniatyrförinställningar för sådana scenarier. I slutet av artikeln finns det ett [exempel på kod](#code_sample) som visar hur du använder Media Services .NET SDK för att utföra kodningsuppgiften.

Mer information om de element som används i exempelförinställningar bör du läsa [Media Encoder Standard-schemat](media-services-mes-schema.md).

Se till att granska avsnittet [Överväganden.](media-services-dotnet-generate-thumbnail-with-mes.md#considerations)
    
## <a name="example-of-a-single-png-file-preset"></a>Exempel på en "enskild PNG-fil"-förinställning

Följande JSON och XML förinställda kan användas för att producera en enda utgång PNG-fil från de första sekunderna av indatavideo, där kodaren gör ett bästa försök att hitta en "intressant" ram. Observera att utdatabilddimensionerna har angetts till 100 %, vilket innebär att dessa matchar dimensionerna för indatavideon. Observera också hur inställningen "Format" i "Utdata" krävs för att matcha användningen av "PngLayers" i avsnittet "Codecs". 

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
    
### <a name="xml-preset"></a>XML-förinställning

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Exempel på en förinställning för "serie JPEG-bilder"

Följande JSON- och XML-förinställning kan användas för att skapa en uppsättning med 10 bilder med tidsstämplar på 5 %, 15 %, ..., 95 % av indatatidslinjen, där bildstorleken anges vara en fjärdedel av indatavideon.

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

### <a name="xml-preset"></a>XML-förinställning
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Exempel på förinställningen "en bild vid en viss tidsstämpel"

Följande JSON- och XML-förinställning kan användas för att skapa en enda JPEG-bild vid 30-sekundersmärket för indatavideon. Den här förinställningen förväntar sig att indatavideon ska vara mer än 30 sekunder lång (annars misslyckas jobbet).

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

### <a name="xml-preset"></a>XML-förinställning
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Exempel på förinställningen "miniatyrer med olika upplösningar"

Följande förinställning kan användas för att generera miniatyrer med olika upplösningar i en uppgift. I exemplet, vid positionerna 5%, 15%, ..., 95% av indata tidslinjen, genererar kodaren två bilder - en på 100% av indatavideoupplösningen och den andra på 50%.

Observera användningen av makrot {Resolution} i filnamnet. Det anger för kodaren att använda den bredd och höjd som du angav i kodningsavsnittet i förinställningen samtidigt som filnamnet på utdatabilderna skapas. Detta hjälper dig också att enkelt skilja mellan de olika bilderna

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

### <a name="xml-preset"></a>XML-förinställning
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Exempel på att generera en miniatyrbild medan kodning

Även om alla ovanstående exempel har diskuterat hur du kan skicka in en kodningsuppgift som bara producerar bilder, kan du också kombinera video/ljudkodning med miniatyrgenerering. Följande JSON- och XML-förinställning talar om för **Media Encoder Standard** att en miniatyrbild ska skapas under kodningen.

### <a name="json-preset"></a><a id="json"></a>JSON förinställda
Information om schema finns i [den här](https://msdn.microsoft.com/library/mt269962.aspx) artikeln.

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

### <a name="xml-preset"></a><a id="xml"></a>XML-förinställning
Information om schema finns i [den här](https://msdn.microsoft.com/library/mt269962.aspx) artikeln.

```csharp
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a name="encode-video-and-generate-thumbnail-with-net"></a><a id="code_sample"></a>Koda video och generera miniatyr med .NET

I följande kodexempel används Media Services .NET SDK för att utföra följande uppgifter:

* Skapa ett kodningsjobb.
* Hämta en referens till Media Encoder Standard-kodaren.
* Läs in den förinställda [XML-](media-services-dotnet-generate-thumbnail-with-mes.md#xml) eller [JSON-koden](media-services-dotnet-generate-thumbnail-with-mes.md#json) som innehåller kodningsförinställningen samt information som behövs för att generera miniatyrer. Du kan spara [XML eller](media-services-dotnet-generate-thumbnail-with-mes.md#xml) [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) i en fil och använda följande kod för att läsa in filen.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Lägg till en enskild kodningsaktivitet i jobbet. 
* Ange den indatatillgång som ska kodas.
* Skapa en utdatatillgång som innehåller den kodade tillgången.
* Lägg till en händelsehanterare för att kontrollera jobbförloppet.
* Skicka in jobbet.

Se [medietjänstutvecklingen med .NET-artikeln](media-services-dotnet-how-to-use.md) för anvisningar om hur du konfigurerar din utvecklingsmiljö.

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

* Användningen av explicita tidsstämplar för Start/Steg/Intervall förutsätter att indatakällan är minst 1 minut lång.
* Element för jpg/png/bmpimage har strängattribut för Start, Steg och Intervall – dessa kan tolkas som:
  
  * Bildrutenummer om de är icke-negativa heltal, till exempel "Start": "120",
  * I förhållande till källans varaktighet om den uttrycks som %-suffixerad, till exempel "Start": "15%", ELLER
  * Tidsstämpel om den uttrycks som HH:MM:SS... . Till exempel "Start": "00:01:00"
    
    Du kan blanda och matcha notationer som du vill.
    
    Dessutom stöder Start också ett speciellt makro:{Bäst}, som försöker fastställa den första "intressanta" bildrutan i innehållet OBS: (Steg och intervall ignoreras när Start är inställt på {Best})
  * Standardvärden: Start:{Bäst}
* Utdataformat måste uttryckligen anges för varje bildformat: Jpg/Png/BmpFormat. När det är dags matchar MES JpgVideo till JpgFormat och så vidare. OutputFormat introducerar ett nytt bildcodec-specifikt makro: {Index}, som måste finnas (en gång och bara en gång) för bildutdataformat.

## <a name="next-steps"></a>Nästa steg

Du kan kontrollera [jobbstatus](media-services-check-job-progress.md) medan kodningsjobbet väntar.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över kodning av Media Services](media-services-encode-asset.md)

