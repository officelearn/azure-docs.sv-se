---
title: Utför avancerad kodning genom att anpassa MES-förinställningar | Microsoft-dokument
description: Det här avsnittet visar hur du utför avancerad kodning genom att anpassa aktivitetsförinställningar för Media Encoder Standard.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 5f7611fd9df207df51fa0e51218d8a234583b1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529791"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Utför avancerad kodning genom att anpassa MES-förinställningar 

## <a name="overview"></a>Översikt

Det här avsnittet visar hur du anpassar förinställningar för Media Encoder Standard. [Kodningen med Media Encoder Standard med hjälp av anpassade förinställningar](media-services-custom-mes-presets-with-dotnet.md) visar hur du använder .NET för att skapa en kodningsuppgift och ett jobb som utför den här uppgiften. När du har anpassat en förinställning anger du de anpassade förinställningarna till kodningsuppgiften. 

Om du använder en XML-förinställning måste du bevara ordningen på elementen, som visas i XML-exempel nedan (keyframeInterval bör till exempel föregå SceneChangeDetection).

> [!NOTE] 
> Många av de avancerade Media Services v2-funktionerna i Media Encoder Standard är för närvarande inte tillgängliga i v3. Mer information finns i [funktionsluckor](https://docs.microsoft.com/azure/media-services/latest/media-services-v2-vs-v3#feature-gaps-with-respect-to-v2-apis).

## <a name="support-for-relative-sizes"></a>Stöd för relativa storlekar

När du skapar miniatyrer behöver du inte alltid ange utdatabredd och höjd i pixlar. Du kan ange dem i procent, i intervallet [1%, ..., 100%].

### <a name="json-preset"></a>JSON förinställda
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>XML-förinställning
    <Width>100%</Width>
    <Height>100%</Height>

## <a name="generate-thumbnails"></a><a id="thumbnails"></a>Skapa miniatyrbilder

I det här avsnittet visas hur du anpassar en förinställning som genererar miniatyrer. Förinställningen som definieras nedan innehåller information om hur du vill koda filen samt information som behövs för att generera miniatyrer. Du kan ta någon av MES förinställningar dokumenteras [detta](media-services-mes-presets-overview.md) avsnitt och lägga till kod som genererar miniatyrer.  

> [!NOTE]
> **Inställningen SceneChangeDetection** i följande förinställning kan bara ställas in på true om du kodar till en enda bithastighetsvideo. Om du kodar till en multibitrat-video och ställer in **SceneChangeDetection** till true returnerar kodaren ett fel.  
>
>

Information om schema finns i [det här](media-services-mes-schema.md) avsnittet.

Se till att granska avsnittet [Överväganden.](#considerations)

### <a name="json-preset"></a><a id="json"></a>JSON förinställda
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


### <a name="xml-preset"></a><a id="xml"></a>XML-förinställning
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

* Användningen av explicita tidsstämplar för Start/Steg/Intervall förutsätter att indatakällan är minst 1 minut lång.
* Element för jpg/png/bmpimage har strängattribut för Start, Steg och Intervall – dessa kan tolkas som:

  * Bildrutenummer om de är icke-negativa heltal, till exempel "Start": "120",
  * I förhållande till källans varaktighet om den uttrycks som %-suffixerad, till exempel "Start": "15%", ELLER
  * Tidsstämpel om den uttrycks som HH:MM:SS... format, till exempel "Start": "00:01:00"

    Du kan blanda och matcha notationer som du vill.

    Dessutom stöder Start också ett speciellt makro:{Bäst}, som försöker fastställa den första "intressanta" bildrutan i innehållet OBS: (Steg och intervall ignoreras när Start är inställt på {Best})
  * Standardvärden: Start:{Bäst}
* Utdataformat måste uttryckligen anges för varje bildformat: Jpg/Png/BmpFormat. När det är dags matchar MES JpgVideo till JpgFormat och så vidare. OutputFormat introducerar ett nytt bildcodec-specifikt makro: {Index}, som måste finnas (en gång och bara en gång) för bildutdataformat.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Trimma en video (urklipp)
I det här avsnittet beskrivs hur kodaren ändras för att klippa eller trimma indatavideon där indata är en så kallad mezzaninfil eller fil på begäran. Kodaren kan också användas för att klippa eller trimma en tillgång, som fångas eller arkiveras från en livestream - detaljerna för detta finns i [den här bloggen](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Om du vill trimma dina videor kan du ta någon av MES-förinställningarna som dokumenterats [i det här](media-services-mes-presets-overview.md) avsnittet och ändra **källelementet** (som visas nedan). Värdet på StartTime måste matcha de absoluta tidsstämplarna för indatavideon. Om till exempel den första bildrutan i indatavideon har en tidsstämpel på 12:00:10.000, ska StartTime vara minst 12:00:10.000 och större. I exemplet nedan antar vi att indatavideon har en starttidsstämpel på noll. **Källor** bör placeras i början av förinställningen.

### <a name="json-preset"></a><a id="json"></a>JSON förinställda
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
Om du vill trimma dina videor kan du ta någon av MES-förinställningarna som dokumenteras [här](media-services-mes-presets-overview.md) och ändra **källelementet** (som visas nedan).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a name="create-an-overlay"></a><a id="overlay"></a>Skapa ett överlägg

Med Media Encoder Standard kan du lägga över en bild på en befintlig video. För närvarande stöds följande format: png, jpg, gif och bmp. Den förinställda som definieras nedan är ett grundläggande exempel på ett videoöverlägg.

Förutom att definiera en förinställd fil måste du också låta Media Services veta vilken fil i tillgången som är överläggsbilden och vilken fil som är källvideon som du vill lägga över bilden på. Videofilen måste vara den **primära** filen.

Om du använder .NET lägger du till följande två funktioner i .NET-exemplet som definieras i [det här](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) avsnittet. **Funktionen UploadMediaFilesFromFolder** laddar upp filer från en mapp (till exempel BigBuckBunny.mp4 och Image001.png) och anger att mp4-filen är den primära filen i tillgången. Funktionen **EncodeWithOverlay** använder den anpassade förinställda filen som skickades till den (till exempel förinställningen som följer) för att skapa kodningsuppgiften.


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
> Nuvarande begränsningar:
>
> Inställningen för överlagringsopacitet stöds inte.
>
> Källvideofilen och överlagringsbildfilen måste finnas i samma tillgång, och videofilen måste anges som den primära filen i den här tillgången.
>
>

### <a name="json-preset"></a>JSON förinställda
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
                  "OverlayLoopCount": 1
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
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
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
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
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


## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Infoga ett tyst ljudspår när ingången inte har något ljud
Om du skickar en indata till kodaren som bara innehåller video och inget ljud innehåller utdatatillgången som standard filer som bara innehåller videodata. Vissa spelare kanske inte kan hantera sådana utdataströmmar. Du kan använda den här inställningen för att tvinga kodaren att lägga till ett tyst ljudspår i utdata i det scenariot.

Om du vill tvinga kodaren att producera en tillgång som innehåller ett tyst ljudspår när ingången inte har något ljud anger du värdet "InsertSilenceIfNoAudio".

Du kan ta någon av MES-förinställningar som dokumenteras i [det här](media-services-mes-presets-overview.md) avsnittet och göra följande ändringar:

### <a name="json-preset"></a>JSON förinställda
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

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Inaktivera automatisk avförstöring
Kunderna behöver inte göra något om de gillar sammanflätning innehållet att automatiskt de-sammanflätade. När den automatiska de-interlacing är på (standard) MES gör automatisk upptäckt av sammanflätade ramar och endast de-sammanflätade ramar markerade som sammanflätade.

Du kan stänga av den automatiska avförstöringen. Det här alternativet rekommenderas inte.

### <a name="json-preset"></a>JSON förinställda
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


## <a name="audio-only-presets"></a><a id="audio_only"></a>Förinställningar för endast ljud
Detta avsnitt visar två endast ljud MES förinställningar: AAC Audio och AAC God kvalitet Audio.

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

### <a name="aac-good-quality-audio"></a>AAC God kvalitet Audio
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

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Sammanfoga två eller flera videofiler

I följande exempel visas hur du kan skapa en förinställning för att sammanfoga två eller flera videofiler. Det vanligaste scenariot är när du vill lägga till ett sidhuvud eller en trailer i huvudvideon. Den avsedda användningen är när de videofiler som redigeras tillsammans delar egenskaper (videoupplösning, bildfrekvens, antal ljudspår, etc.). Du bör noga med att inte blanda videor med olika bildfrekvens, eller med olika antal ljudspår.

>[!NOTE]
>Den nuvarande utformningen av sammanfogning funktionen förväntar sig att indata videoklipp är konsekventa när det gäller upplösning, bildhastighet etc. 

### <a name="requirements-and-considerations"></a>Krav och överväganden

* Indatavideor bör bara ha ett ljudspår.
* Indatavideor bör alla ha samma bildhastighet.
* Du måste ladda upp dina videor till separata tillgångar och ange videorna som den primära filen i varje tillgång.
* Du måste veta hur länge dina videor är.
* De förinställda exemplen nedan förutsätter att alla indatavideor börjar med en tidsstämpel på noll. Du måste ändra StartTime-värdena om videorna har olika starttidsstämpel, vilket vanligtvis är fallet med livearkiv.
* JSON-förinställningen innehåller uttryckliga hänvisningar till AssetID-värdena för indatatillgångarna.
* Exempelkoden förutsätter att JSON-förinställningen har sparats i en lokal fil, till exempel "C:\supportFiles\preset.json". Det förutsätter också att två tillgångar har skapats genom att ladda upp två videofiler och att du känner till de resulterande AssetID-värdena.
* Kodavsnittet och JSON-förinställningen visar ett exempel på att sammanfoga två videofiler. Du kan utöka den till fler än två videor genom att:

  1. Anropar uppgift. InputAssets.Add() upprepade gånger för att lägga till fler videor, i ordning.
  2. Göra motsvarande ändringar i "Källor" elementet i JSON, genom att lägga till fler poster, i samma ordning.

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

### <a name="json-preset"></a>JSON förinställda

Uppdatera din anpassade förinställning med id:n för de tillgångar som du vill sammanfoga och med rätt tidssegment för varje video.

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

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Beskär videoklipp med Media Encoder Standard
Se [avsnittet Beskär videor med Media Encoder Standard.](media-services-crop-video.md)

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Infoga ett videospår när indata inte har någon video

Om du skickar en indata till kodaren som bara innehåller ljud och ingen video innehåller utdatatillgången som standard filer som bara innehåller ljuddata. Vissa spelare, inklusive Azure Media Player (se [detta)](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)kanske inte kan hantera sådana strömmar. Du kan använda den här inställningen för att tvinga kodaren att lägga till ett svartvitt videospår i utdata i det scenariot.

> [!NOTE]
> Om du tvingar kodaren att infoga ett utdatavideospår ökar storleken på utdatatillgången och därmed kostnaden för kodningsaktiviteten. Du bör köra tester för att kontrollera att denna resulterande ökning har endast en blygsam inverkan på dina månadsavgifter.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Infoga video på endast den lägsta bithastigheten

Anta att du använder en flerbithastighetskodningsförinställning, till exempel ["H264 Multiple Bitrate 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) för att koda hela indatakatalogen för direktuppspelning, som innehåller en blandning av videofiler och ljudfiler. I det här fallet, när ingången inte har någon video, kanske du vill tvinga kodaren att infoga ett svartvitt videospår på bara den lägsta bithastigheten, i motsats till att infoga video vid varje utdatabithastighet. För att uppnå detta måste du använda flaggan **InsertBlackIfNoVideoBottomLayerOnly.**

Du kan ta någon av MES-förinställningar som dokumenteras i [det här](media-services-mes-presets-overview.md) avsnittet och göra följande ändringar:

#### <a name="json-preset"></a>JSON förinställda
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-förinställning

När du använder XML använder du Condition="InsertBlackIfNoVideoBottomLayerOnly" som attribut till **H264Video-elementet** och Condition="InsertSilenceIfNoAudio" som attribut till **AACAudio**.

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

### <a name="inserting-video-at-all-output-bitrates"></a>Infoga video vid alla utdatabithastigheter
Anta att du använder en flerbithastighetskodningsförinställning, till exempel ["H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) för att koda hela indatakatalogen för direktuppspelning, som innehåller en blandning av videofiler och ljudfiler. I det här fallet när ingången inte har någon video, kanske du vill tvinga kodaren att infoga ett svartvitt videospår på alla utdatabithastigheter. Detta säkerställer att dina utdatatillgångar är alla homogena med avseende på antalet videospår och ljudspår. För att uppnå detta måste du ange flaggan "InsertBlackIfNoVideo".

Du kan ta någon av MES-förinställningar som dokumenteras i [det här](media-services-mes-presets-overview.md) avsnittet och göra följande ändringar:

#### <a name="json-preset"></a>JSON förinställda
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-förinställning

När du använder XML använder du Condition="InsertBlackIfNoVideo" som attribut till **H264Video-elementet** och Condition="InsertSilenceIfNoAudio" som attribut till **AACAudio**.

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

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Rotera en video
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) stöder rotation genom vinklar på 0/90/180/270. Standardbeteendet är "Auto", där den försöker identifiera rotationsmetadata i den inkommande videofilen och kompensera för den. Inkludera följande **källelement** i en av de förinställningar som definieras i [det här](media-services-mes-presets-overview.md) avsnittet:

### <a name="json-preset"></a>JSON förinställda
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

Se även [det här](media-services-mes-schema.md#PreserveResolutionAfterRotation) avsnittet för mer information om hur kodaren tolkar inställningarna för bredd och höjd i förinställningen när rotationskompensation utlöses.

Du kan använda värdet "0" för att ange för kodaren att ignorera rotationsmetadata, om sådana finns, i indatavideon.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över kodning av Media Services](media-services-encode-asset.md)
