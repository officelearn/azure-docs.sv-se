---
title: Så här beskär du videor med Media Encoder Standard - Azure | Microsoft-dokument
description: Beskärning är processen att välja ett rektangulärt fönster i videoramen och bara koda pixlarna i det fönstret. Den här artikeln visar hur du beskära videor med Media Encoder Standard.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 059816284e39c65bb772bd02f066d73da624722f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887772"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Beskär videoklipp med Media Encoder Standard  

Du kan använda MEDIA Encoder Standard (MES) för att beskära indatavideon. Beskärning är processen att välja ett rektangulärt fönster i videoramen och bara koda pixlarna i det fönstret. Följande diagram illustrerar processen.

![Beskära en video](./media/media-services-crop-video/media-services-crop-video01.png)

Anta att du har som indata en video som har en upplösning på 1920x1080 pixlar (16:9 bildförhållande), men har svarta staplar (pelare lådor) till vänster och höger, så att endast en 4:3 fönster eller 1440x1080 pixlar innehåller aktiv video. Du kan använda MES för att beskära eller redigera ut de svarta fälten och koda regionen 1440x1080.

Beskärning i MES är ett förbearbetningssteg, så beskärningsparametrarna i kodningsförinställningen gäller för den ursprungliga indatavideon. Kodning är ett efterföljande steg och inställningarna för bredd/höjd gäller för den *förbearbetade* videon och inte på den ursprungliga videon. När du utformar förinställningen måste du göra följande: (a) välj beskärningsparametrar baserat på den ursprungliga indatavideon och (b) välja kodinställningarna baserat på den beskurna videon. Om du inte matchar kodinställningarna med den beskurna videon blir utdata inte som du förväntar dig.

[I följande](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) avsnitt visas hur du skapar ett kodningsjobb med MES och hur du anger en anpassad förinställning för kodningsaktiviteten. 

## <a name="creating-a-custom-preset"></a>Skapa en anpassad förinställning
I exemplet som visas i diagrammet:

1. Originalingång är 1920x1080
2. Den måste beskäras till en utgång på 1440x1080, som är centrerad i inmatningsramen
3. Detta innebär en X-förskjutning på (1920 – 1440)/2 = 240 och en Y-förskjutning på noll
4. Grödarektangelns bredd och höjd är 1440 respektive 1080
5. I kodstadiet är asken att producera tre lager, är upplösningar 1440x1080, 960x720 respektive 480x360

### <a name="json-preset"></a>JSON förinställda
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
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
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
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
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
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


## <a name="restrictions-on-cropping"></a>Begränsningar för beskärning
Beskärningsfunktionen är avsedd att vara manuell. Du skulle behöva ladda indatavideon till ett lämpligt redigeringsverktyg som låter dig välja ramar av intresse, placera markören för att bestämma förskjutningar för beskärningsrektangeln, för att bestämma kodningsförinställningen som är inställd för just den videon, etc. Den här funktionen är inte avsedd att aktivera saker som: automatisk identifiering och borttagning av svarta brevlåde-/pelarboxkanter i indatavideon.

Följande begränsningar gäller för beskärningsfunktionen. Om dessa inte uppfylls kan kodningsaktiviteten misslyckas eller skapa ett oväntat utdata.

1. Samkräxinaterna och storleken på croprektangeln måste passa in i indatavideon
2. Som nämnts ovan måste bredden & höjd i kodinställningarna motsvara den beskurna videon
3. Beskärning gäller videor som tagits i liggande läge (dvs. inte tillämplig på videor som spelats in med en smartphone som hålls vertikalt eller i stående läge)
4. Fungerar bäst med progressiv video som fångas med fyrkantiga pixlar

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Nästa steg
Se Utbildningsvägarna för Azure Media Services som hjälper dig att lära dig mer om fantastiska funktioner som erbjuds av AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
