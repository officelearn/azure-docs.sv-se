---
title: Utföra avancerade encoding genom att anpassa MES-förinställningar | Microsoft Docs
description: Det här avsnittet visar hur du utför avancerad kodning genom att anpassa förval för Media Encoder Standard uppgift.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: 4601628fd0fbdbd80c2b159a4578e25cb4e3c4c5
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250775"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Utföra avancerade encoding genom att anpassa MES-förinställningar 

## <a name="overview"></a>Översikt

Det här avsnittet visar hur du anpassar förinställningar för Media Encoder Standard. Den [Encoding med Media Encoder Standard med anpassade förinställningar](media-services-custom-mes-presets-with-dotnet.md) avsnittet visar hur du använder .NET för att skapa ett kodningsjobb och ett jobb som kör den här uppgiften. När du har ändrat en förinställning kan du ange anpassade förinställningar för kodning uppgift. 

>[!NOTE]
>Om du använder en XML-förinställning, se till att bevara ordningen på element, som visas i XML-exemplen nedan (till exempel KeyFrameInterval bör föregå SceneChangeDetection).
>

I det här avsnittet är de anpassade förinställningar som utför följande kodningsuppgifter visas.

## <a name="support-for-relative-sizes"></a>Stöd för relativa storleken

När du genererar miniatyrer, behöver du inte alltid ange utdata bredd och höjd i bildpunkter. Du kan ange dem i procent i intervallet [% 1,..., 100%].

### <a name="json-preset"></a>JSON-förinställning
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>XML-förinställning
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>Generera miniatyrer

Det här avsnittet visar hur du anpassar en förinställning som skapar miniatyrer. Den förinställning som definieras nedan innehåller information om hur du vill koda din fil samt information som behövs för att generera miniatyrer. Du kan vidta någon av MES-förinställningar dokumenterade [detta](media-services-mes-presets-overview.md) och lägger till kod som skapar miniatyrer.  

> [!NOTE]
> Den **SceneChangeDetection** inställning i den följande förinställningen kan endast anges till true om du kodar du en enkel bithastighet video. Om du kodar till en video med flera bithastigheter och set **SceneChangeDetection** till SANT, vilken kodare som returnerar ett fel.  
>
>

Läs om hur schemat [detta](media-services-mes-schema.md) avsnittet.

Se till att granska den [överväganden](#considerations) avsnittet.

### <a id="json"></a>JSON-förinställning
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
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
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
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a id="xml"></a>XML-förinställning
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
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

### <a name="considerations"></a>Överväganden

Följande gäller:

* Användningen av explicita tidsstämplar för Start/steg/intervall förutsätter att Indatakällan är minst 1 minut.
* JPG/Png/BmpImage element har Start, steg, och vara strängattribut – dessa kan tolkas som:

  * Bildrutenummer om de är icke-negativa heltal, till exempel ”Start”: ”120”
  * Relativt källa varaktighet om det uttrycks som %-suffix, till exempel ”Start”: ”15%”, eller
  * Tidsstämpel om det uttrycks som: mm: ss... Formatera, till exempel ”Start” ”: 00: 01:00”

    Du kan blanda och matcha beteckningar som du finns.

    Dessutom Start även stöd för ett särskilt makro: {bästa}, som försöker bestämma första ”intressanta” bildruta anteckningens innehåll: (steg och intervallet ignoreras när Start är inställd på {bästa})
  * Standard: Starta: {bästa}
* Utdataformat måste anges uttryckligen för varje bildformat: Jpg/Png/BmpFormat. När det finns, matchar MES JpgVideo till JpgFormat och så vidare. OutputFormat introducerar en ny bild-codec visst makro: {Index}, som måste vara presentera (en gång och bara en gång) för bild-utdataformat.

## <a id="trim_video"></a>Beskär video (urklippet)
Det här avsnittet berättar om hur du ändrar encoder förinställningar för att klippa ut eller trimma indatavideon där indata är en s.k. mezzaninfil eller på begäran-fil. Kodaren kan också användas för att klippa ut eller trimma en tillgång som avbildas eller arkiveras från en direktsänd dataström – information för detta finns i [den här bloggen](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Om du vill trimma dina videor, du kan vidta någon av MES-förinställningar dokumenterade [detta](media-services-mes-presets-overview.md) avsnittet och ändra den **källor** element (som visas nedan). Värdet för StartTime måste matcha absolut tidsstämplarna på indatavideon. Till exempel om den första bildrutan på indatavideon har en tidsstämpel för 12:00:10.000, sedan StartTime bör vara minst 12:00:10.000 och senare. I exemplet nedan förutsätter vi att indatavideon har en tidsstämpel som från noll. **Källor** ska placeras i början av förinställningen.

### <a id="json"></a>JSON-förinställning
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="xml-preset"></a>XML-förinställning
Om du vill trimma dina videor, du kan vidta någon av MES-förinställningar dokumenterade [här](media-services-mes-presets-overview.md) och ändra den **källor** element (som visas nedan).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
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
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a id="overlay"></a>Skapa ett överlägg

Media Encoder Standard kan du överlappar en avbildning till en befintlig video. Stöds för närvarande följande format: png, jpg, gif, och bmp. Den förinställning som anges nedan är ett grundläggande exempel på en videoöverlägg.

Förutom att definiera en förvalda, måste du också kan Media Services vet vilken fil i tillgången är överlägget avbildningen och vilken fil är videon som du vill överlappar bilden. Videofilen måste vara den **primära** fil.

Om du använder .NET, lägger du till följande två funktioner i .NET-exempel som definierats i [detta](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) avsnittet. Den **UploadMediaFilesFromFolder** funktionen Överför filer från en mapp (till exempel BigBuckBunny.mp4 och Image001.png) och anger mp4-fil ska vara den primära filen i tillgången. Den **EncodeWithOverlay** funktionen använder den anpassade förvalda som skickades till den (till exempel den förinställning som följer) så här skapar du den kodning.


    static public IAsset UploadMediaFilesFromFolder(string folderPath)
    {
        IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
    
        foreach (var af in asset.AssetFiles)
        {
            // The following code assumes 
            // you have an input folder with one MP4 and one overlay image file.
            if (af.Name.Contains(".mp4"))
                af.IsPrimary = true;
            else
                af.IsPrimary = false;
    
            af.Update();
        }
    
        return asset;
    }

    static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("Media Encoder Standard Job");
        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(customPresetFileName);

        // Create a task
        ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input assets to be encoded.
        // This asset contains a source file and an overlay file.
        task.InputAssets.Add(assetSource);

        // Add an output asset to contain the results of the job. 
        task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }


> [!NOTE]
> Aktuella begränsningar:
>
> Inställningen för opacitet överlägget stöds inte.
>
> Din video källfil och överlägg image-filen måste vara i samma tillgång och videofilen måste anges som den primära filen i den här tillgången.
>
>

### <a name="json-preset"></a>JSON-förinställning
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="xml-preset"></a>XML-förinställning
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


## <a id="silent_audio"></a>Infoga en tyst ljudspår när indata har inget ljud
Som standard om du skickar indata till den kodare som innehåller endast video och inga ljud innehåller utdatatillgången filer som innehåller endast video data. Vissa spelare kanske inte kan hantera sådana utdataströmmar. Du kan använda den här inställningen för att tvinga kodaren att lägga till en tyst ljudspår till utdata i det här scenariot.

Ange värdet som ”InsertSilenceIfNoAudio” om du vill framtvinga kodaren att skapa en tillgång som innehåller en tyst ljudspår när indata har inget ljud.

Du kan vidta någon av MES-förinställningar dokumenterade i [detta](media-services-mes-presets-overview.md) avsnittet och gör följande ändringar:

### <a name="json-preset"></a>JSON-förinställning
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>XML-förinställning
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a id="deinterlacing"></a>Inaktivera automatisk ta bort färgsammanflätning
Kunder behöver inte göra något om de gillar interlace innehållet till att automatiskt ta bort sammanflätade. När den automatiskt ta bort sammanflätning är aktiverat (standard) inte Autoidentifiering av sammanflätade bildrutor MES och bara ta bort interlaces ramar som har markerats som sammanflätad.

Du kan inaktivera den automatiskt ta bort sammanflätning. Det här alternativet rekommenderas inte.

### <a name="json-preset"></a>JSON-förinställning
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>XML-förinställning
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a id="audio_only"></a>Enbart ljud förinställningar
Det här avsnittet beskrivs två ljuddata MES-förinställningar: AAC ljud- och AAC god kvalitet ljud.

### <a name="aac-audio"></a>AAC-ljud
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="aac-good-quality-audio"></a>AAC god kvalitet ljud
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="concatenate"></a>Sammanfoga två eller flera videofiler

I följande exempel visas hur du kan generera en förinställning för att sammanfoga två eller flera videofiler. Det vanligaste scenariot är när du vill lägga till ett sidhuvud eller en trailer huvudsakliga videon. Den tänkta användningen är när videofiler som redigeras tillsammans resursegenskaper (skärmupplösning, bildfrekvens, ljudspår antal osv.). Det är viktigt att inte blanda videor av olika bildrutehastigheter eller med olika antal ljudspår.

>[!NOTE]
>Den aktuella designen av funktionen sammanfogning förväntar sig att de inkommande videoklipp är konsekventa när det gäller upplösning som är bildfrekvens osv. 

### <a name="requirements-and-considerations"></a>Krav och överväganden

* Inkommande videor bör bara ha ett ljudspår.
* Ange videor måste ha samma bildfrekvens.
* Du måste överföra dina videor till separata tillgångar och Ställ in videor som den primära filen i varje tillgång.
* Du behöver veta varaktigheten för dina videor.
* Förinställda exemplen nedan förutsätter att alla indata-videor som börjar med en tidsstämpel noll. Du behöver ändra värdena StartTime om videor har olika från tidsstämpel, som normalt är fallet med direktsända strömningar.
* JSON-förinställningen gör explicita referenser till AssetID värdena för resurser som indata.
* Exempelkoden förutsätter att JSON-förinställning har sparats till en lokal fil, till exempel ”C:\supportFiles\preset.json”. Den förutsätter också att två tillgångar har skapats genom att ladda upp två videofiler och att du vet vilka resulterande AssetID-värden.
* Kodfragmentet och JSON förinställning visar ett exempel på att sammanfoga två videofiler. Du kan utöka det till fler än två videor genom att:

  1. Anropar uppgiften. InputAssets.Add() flera gånger för att lägga till fler videor i ordning.
  2. Gör motsvarande redigerar i elementet ”källor” i JSON, genom att lägga till fler poster i samma ordning.

### <a name="net-code"></a>.NET-kod

    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job.
    // This output is specified as AssetCreationOptions.None, which
    // means the output asset is not encrypted.
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

### <a name="json-preset"></a>JSON-förinställning

Uppdatera din egen förinställning med ID: n för de resurser som du vill sammanfoga och med lämplig tidpunkt segment för varje video.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
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
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="crop"></a>Beskär videoklipp med Media Encoder Standard
Se den [beskära videoklipp med Media Encoder Standard](media-services-crop-video.md) avsnittet.

## <a id="no_video"></a>Infoga en video spåra när indata har ingen bild

Som standard om du skickar indata till den kodare som innehåller endast ljud och ingen bild innehåller utdatatillgången filer som innehåller endast ljuddata. Vissa spelare, inklusive Azure Media Player (se [detta](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) kanske inte kan hantera sådana strömmar. Du kan använda den här inställningen för att tvinga kodaren att lägga till ett svartvit video spår till utdata i det här scenariot.

> [!NOTE]
> Tvinga kodaren att infoga en video Spåra utdata ökar storleken på utdata tillgången, och därmed kostnaden som tillkommer för aktiviteten kodning. Du bör köra tester för att kontrollera att denna gällande ökning har endast en liten inverkan på dina månatliga debiteringar.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Infoga video på lägsta bithastigheten

Anta att du med flera bithastigheter kodning förinställning som [”H264, flera bithastigheter, 720p”](media-services-mes-preset-h264-multiple-bitrate-720p.md) att koda hela inkommande katalogen för strömning, som innehåller en blandning av video och ljud endast filer. I det här scenariot när indata har ingen bild kan du tvinga kodaren att infoga ett svartvit video spår på bara lägsta bithastigheten, till skillnad från Infoga video vid varje bithastigheter för utdata. Om du vill göra detta kan du behöva använda den **InsertBlackIfNoVideoBottomLayerOnly** flaggan.

Du kan vidta någon av MES-förinställningar dokumenterade i [detta](media-services-mes-presets-overview.md) avsnittet och gör följande ändringar:

#### <a name="json-preset"></a>JSON-förinställning
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-förinställning

Använd villkor när du använder XML = ”InsertBlackIfNoVideoBottomLayerOnly” som ett attribut till den **H264Video** element och villkor = ”InsertSilenceIfNoAudio” som ett attribut till **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Infoga video på alla utdata bithastigheter för utdata
Anta att du med flera bithastigheter kodning förinställning som [”H264, flera bithastigheter, 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) att koda hela inkommande katalogen för strömning, som innehåller en blandning av video och ljud endast filer. I det här scenariot när indata har ingen bild kan du tvinga kodaren att infoga svartvit video reda på alla utdata bithastighet. Detta säkerställer att dina utdata tillgångar är alla homogen med avseende på antal video spårar och ljudspår. För att uppnå detta, måste du ange flaggan ”InsertBlackIfNoVideo”.

Du kan vidta någon av MES-förinställningar dokumenterade i [detta](media-services-mes-presets-overview.md) avsnittet och gör följande ändringar:

#### <a name="json-preset"></a>JSON-förinställning
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-förinställning

Använd villkor när du använder XML = ”InsertBlackIfNoVideo” som ett attribut till den **H264Video** element och villkor = ”InsertSilenceIfNoAudio” som ett attribut till **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a id="rotate_video"></a>Rotera en video
Den [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) stöder rotation av vinklar för 0/90/180/270. Standardinställningen är ”automatisk”, där den försöker identifiera rotation metadata i inkommande videofilen och kompensera för den. Inkluderar följande **källor** element till ett förinställningar som definierats i [detta](media-services-mes-presets-overview.md) avsnittet:

### <a name="json-preset"></a>JSON-förinställning
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...
### <a name="xml-preset"></a>XML-förinställning
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Se även [detta](media-services-mes-schema.md#PreserveResolutionAfterRotation) mer information om hur kodaren tolkar bredd och höjd inställningarna i förinställningen när rotation kompensation utlöses.

Du kan använda värdet ”0” för att indikera till kodaren att ignorera rotation metadata, om den finns i inkommande video.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Media Services-kodning – översikt](media-services-encode-asset.md)
