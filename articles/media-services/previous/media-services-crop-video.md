---
title: Hur du beskär videor med Media Encoder Standard - Azure | Microsoft Docs
description: Den här artikeln visar hur du beskär videor med Media Encoder Standard.
services: media-services
documentationcenter: ''
author: anilmur
manager: cfowler
editor: ''
ms.assetid: 7628f674-2005-4531-8b61-d7a4f53e46ba
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2017
ms.author: anilmur;juliako;
ms.openlocfilehash: 2592316481c9e265fbae20b832beb21ae4905b14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="crop-videos-with-media-encoder-standard"></a>Beskär videoklipp med Media Encoder Standard
Du kan använda Media Encoder Standard (MES) Beskär indata video. Beskär är en process för att markera ett rektangulärt fönster i videon bildruta och kodning bara bildpunkter i fönstret. Följande diagram hjälper illustrerar processen.

![Beskär en video](./media/media-services-crop-video/media-services-crop-video01.png)

Anta att du har som indata en video som har en upplösning på 1 920 x 1 080 bildpunkter (proportionerna 16:9), men har svarta fält (pelare rutor) på vänster och höger, så att endast en 4:3-fönstret eller 1440 x 1 080 bildpunkter innehåller active video. Du kan använda MES Beskär eller redigera ut de svarta fält och koda 1440 x 1 080 region.

Beskär i MES är ett före bearbetning skede, så beskärningsverktyget parametrarna i den kodning förinställningen som gäller för den ursprungliga indatavideo. Kodning är ett efterföljande steg och bredd och höjd inställningar gäller för den *bearbetas före* video- och inte till den ursprungliga videon. När du utformar din förinställda måste du göra följande: a väljer parametrarna Beskär baserat på den ursprungliga indatavideo och (b) din koda inställningar baserat på beskuren videon. Om du inte matchar din koda inställningar till beskuren video, utdata blir inte som förväntat.

Den [följande](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) avsnittet visar hur du skapar ett kodningsjobb med MES och hur du anger en egen förinställning för kodning uppgiften. 

## <a name="creating-a-custom-preset"></a>Skapa en anpassad förinställning
I exemplet som visas i diagrammet:

1. Ursprungliga indata är 1 920 x 1 080
2. Behöver beskäras till utdata för 1440 x 1 080, som är uppbyggd i den inkommande ram
3. Detta innebär en X-förskjutning av (1 920 – 1 440) / 2 = 240 och en Y-förskjutning noll
4. Bredden och höjden rektangelns Beskär är 1440 1080, respektive
5. I steget koda be är att skapa tre lager, är lösningar 1440 x 1 080 960 x 720 och 480 x 360 respektive

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


## <a name="restrictions-on-cropping"></a>Begränsningar för Beskär
Funktionen beskärningsverktyget är avsedd att vara manuell. Du skulle behöva läsa in din indatavideo till ett lämpligt redigeringsverktyg som låter dig välja ramar intressanta, placera markören för att fastställa förskjutningar för beskärningsrektangelns att fastställa den kodning förinställning som är anpassad för att viss video, etc. Den här funktionen är inte avsedd att till exempel: Automatisk identifiering och borttagning av svart letterbox/pillarbox-format kantlinjer i din videoinmatning.

Följande begränsningar gäller för funktionen beskärningsverktyget. Om dessa inte är uppfyllt, koda uppgiften misslyckas eller ett oväntat resultat.

1. Koordinater och storleken på Beskär rektangeln måste passa i den inkommande videon
2. Som nämnts ovan är har bredd och höjd i inställningarna för koda motsvarar beskuren videon
3. Beskär gäller videor som fångades i liggande läge (dvs. kan inte användas på videor som registrerats med en smartphone hålls lodrätt eller stående)
4. Fungerar bäst med progressiv video till med kvadratisk bildpunkter

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Nästa steg
Se Azure Media Services utbildningsvägar för att du lär dig mer om funktionerna som erbjuds av AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
