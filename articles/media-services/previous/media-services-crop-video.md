---
title: Så här beskära videoklipp med Media Encoder Standard - Azure | Microsoft Docs
description: Den här artikeln visar hur du beskära videoklipp med Media Encoder Standard.
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
ms.date: 29/10/2018
ms.author: anilmur;juliako;
ms.openlocfilehash: f6c853648b138763675e016d8de6eaff6377f166
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229608"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Beskär videoklipp med Media Encoder Standard
Du kan använda Media Encoder Standard (MES) för att beskära dina indata video. Beskära är processen för att välja ett rektangulärt fönster inom ramen för video och kodning bara bildpunkter i fönstret. Följande diagram hjälper illustrerar processen.

![Beskär en video](./media/media-services-crop-video/media-services-crop-video01.png)

Anta att du har som indata en video som har en upplösning på 1 920 x 1 080 bildpunkter (bredd-höjd-förhållandet 16:9), men har svarta fält (pelare rutor) till vänster och höger, så att endast en 4:3-fönstret eller 1 440 x 1 080 bildpunkter innehåller active video. Du kan använda MES beskära eller redigera ut de svarta fält och koda regionen 1 440 x 1 080.

Beskärning i MES är ett bearbetnings före steg så beskärning parametrarna i förinställningen för kodningen gäller för den ursprungliga indatavideon. Kodning är ett efterföljande steg och bredd och höjd-inställningar gäller för den *bearbetas före* video, och inte till den ursprungliga videon. När du utformar din förinställda behöver du följande: (a) Välj parametrarna Beskär baserat på den ursprungliga indatavideon och (b) Välj din koda inställningar baserat på beskurna videon. Om du inte överensstämmer med dina koda inställningar till beskurna videon, utdata kan inte som förväntat.

Den [följande](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) avsnittet visar hur du skapar ett kodningsjobb med MES och hur du anger en anpassad förinställning för kodning uppgiften. 

## <a name="creating-a-custom-preset"></a>Skapa en anpassad förinställning
I exemplet som visas i diagrammet:

1. Ursprungliga indata är 1 920 x 1 080
2. Den behöver beskäras utdata 1 440 x 1080, som centreras i ramen indata
3. Det innebär att en X-förskjutning av (1 920 – 1 440) / 2 = 240 och en Y-förskjutning noll
4. Bredden och höjden rektangelns Beskär är 1440 1080, respektive
5. I koda-fasen i fråga är att skapa tre lager, är lösningar 1 440 x 1 080 960 x 720 och 480 x 360, respektive

### <a name="json-preset"></a>JSON-förinställning
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
Funktionen beskärning är avsedd att vara manuell. Du skulle behöva läsa in din indatavideo i ett lämpligt redigeringsverktyg som låter dig välja bildrutor i närheten, placerar du markören för att fastställa förskjutningar för beskärning rektangeln fastställa förinställningen för kodningen som är anpassad till den specifika video, osv. Den här funktionen är inte avsedd att möjliggöra t.ex.: Automatisk identifiering och borttagning av svart brevlåda/pillarbox-format kantlinjer i din videoinmatning.

Följande begränsningar gäller för funktionen beskärning. Om dessa inte uppfylls, koda aktivitet misslyckas eller ett oväntat resultat.

1. Koordinater och storleken på rektangeln Beskär måste rymmas inom indatavideon
2. Som nämnts ovan är måste bredd och höjd i inställningarna för koda motsvara beskurna videon
3. Beskära gäller videor som avbildas i liggande läge (dvs. inte tillämpligt för videor som registrerats med en smartphone hålls lodrätt eller i stående läge)
4. Fungerar bäst med progressiv video med kvadratisk bildpunkter

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Nästa steg
Se Azure Media Services utbildningsvägar för att lära dig om funktionerna som erbjuds av AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
