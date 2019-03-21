---
title: Aktiviteten förinställningar för Media Encoder Standard (MES) | Microsoft Docs
description: Avsnittet ger och översikt över exemplet definierade förinställningar för Media Encoder Standard (MES).
author: Juliako
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 50c52369a5a957a4dd6279cac5079e2dea023106
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260034"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Exemplet förinställningar för Media Encoder Standard (MES)

**Media Encoder Standard** definierar en uppsättning fördefinierade system kodning förinställningar som du kan använda när du skapar kodningsjobb. Det rekommenderas att använda den ”Adaptiv direktuppspelning” förinställning om du vill koda en video för direktuppspelning med Media Services. När du anger detta förinställda, Media Encoder Standard tar [Autogenerera en bithastighetsstege](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Skapa anpassade förinställningar från exempel
Media Services stöder helt anpassa alla värden i förinställningar för att uppfylla dina specifika kodning behov och krav. Om du vill anpassa en förinställningen för kodningen kan du börja med en av de nedan systeminställningar som tillhandahålls i det här avsnittet som en mall för din anpassade konfiguration. Förklaringar av vad varje element i dessa förinställningar innebär och de giltiga värdena för varje element finns i den [Media Encoder Standard schemat](media-services-mes-schema.md) avsnittet.  
  
> [!NOTE]
>  När du använder en förinställning för 4k kodar, bör du få den `S3` reserverade enhetstypen. Mer information finns i [så skala kodning](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Rotera videoklippet standardinställningen i förinställningar:
När du arbetar med Media Encoder Standard är video rotation aktiverat som standard. Om din video har registrerats på en mobil enhet i stående läge, sedan roteras dessa förinställningar dem till liggande läge före kodning.
 
## <a name="available-presets"></a>Tillgängliga förinställningar: 

 [H264 Multibithastighet 1080p, Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) producerar en uppsättning 8 GOP-justerad MP4-filer, sträcker sig från 6000 kbit/s till 400 kbit/s och AAC 5.1 ljud.  
  
 [H264 Multibithastighet 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) producerar en uppsättning 8 GOP-justerad MP4-filer, sträcker sig från 6000 kbit/s till 400 kbit/s och stereo AAC-ljud.  
  
 [H264, flera bithastigheter, 16 x 9 för iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) producerar en uppsättning 8 GOP-justerad MP4-filer, sträcker sig från 8500 kbit/s till 200 kbit/s och stereo AAC-ljud.  
  
 [H264, flera bithastigheter, 16 x 9 SD, Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) producerar en uppsättning 5 GOP-justerad MP4-filer, mellan 1900 kbit/s och 400 kbit/s och AAC 5.1 ljud.  
  
 [H264, flera bithastigheter, 16 x 9, SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) producerar en uppsättning 5 GOP-justerad MP4-filer, mellan 1900 kbit/s och 400 kbit/s och stereo AAC-ljud.  
  
 [H264, flera bithastigheter 4K, Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) producerar en uppsättning 12 GOP-justerad MP4-filer, mellan 20000 kbit/s och 1 000 kbit/s och AAC 5.1 ljud.  
  
 [H264, flera bithastigheter, 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) producerar en uppsättning 12 GOP-justerad MP4-filer, mellan 20000 kbit/s och 1 000 kbit/s och stereo AAC-ljud.  
  
 [H264, flera bithastigheter, 4 x 3 för iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) producerar en uppsättning 8 GOP-justerad MP4-filer, sträcker sig från 8500 kbit/s till 200 kbit/s och stereo AAC-ljud.  
  
 [H264, flera bithastigheter, 4 x 3 SD, Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) producerar en uppsättning 5 GOP-justerad MP4-filer, sträcker sig från 1600 kbit/s till 400 kbit/s och AAC 5.1 ljud.  
  
 [H264, flera bithastigheter, 4 x 3, SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) producerar en uppsättning 5 GOP-justerad MP4-filer, sträcker sig från 1600 kbit/s till 400 kbit/s och stereo AAC-ljud.  
  
 [H264, flera bithastigheter 720p, Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) producerar en uppsättning 6 GOP-justerad MP4-filer, sträcker sig från 3400 kbit/s till 400 kbit/s och AAC 5.1 ljud.  
  
 [H264, flera bithastigheter, 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) producerar en uppsättning 6 GOP-justerad MP4-filer, sträcker sig från 3400 kbit/s till 400 kbit/s och stereo AAC-ljud.  
  
 [H264, enkel bithastighet 1080p, Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) producerar en enda MP4-fil med en bithastighet 6750 kbit/s och AAC 5.1 ljud.  
  
 [H264, enkel bithastighet, 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) producerar en enda MP4-fil med en bithastighet 6750 kbit/s och stereo AAC-ljud.  
  
 [H264, enkel bithastighet, 4K, Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) producerar en enda MP4-fil med en bithastighet 18000 kbit/s och AAC 5.1 ljud.  
  
 [H264, enkel bithastighet, 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) producerar en enda MP4-fil med en bithastighet 18000 kbit/s och stereo AAC-ljud.  
  
 [H264, enkel bithastighet, 4 x 3 SD, Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) producerar en enda MP4-fil med en bithastighet 1800 kbit/s och AAC 5.1 ljud.  
  
 [H264, enkel bithastighet, 4 x 3, SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) producerar en enda MP4-fil med en bithastighet 1800 kbit/s och stereo AAC-ljud.  
  
 [H264, enkel bithastighet, 16 x 9 SD, Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) producerar en enda MP4-fil med en bithastighet 2200 kbit/s och AAC 5.1 ljud.  
  
 [H264, enkel bithastighet, 16 x 9, SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) producerar en enda MP4-fil med en bithastighet 2200 kbit/s och stereo AAC-ljud.  
  
 [H264, enkel bithastighet 720p, Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) producerar en enda MP4-fil med en bithastighet 4500 kbit/s och AAC 5.1 ljud.  
  
 [H264, enkel bithastighet, 720p för Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) förinställningen skapar en enda MP4-fil med en bithastighet 2000 kbit/s och stereo AAC.  
  
 [H264, enkel bithastighet, 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) producerar en enda MP4-fil med en bithastighet 4500 kbit/s och stereo AAC-ljud.  
  
 [H264 enkel bithastighet högkvalitativa SD för Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) producerar en enda MP4-fil med en bithastighet 500 kbit/s och stereo AAC-ljud...  
  
 [H264 enkel bithastighet låg kvalitet SD för Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) producerar en enda MP4-fil med en bithastighet 56 kbit/s och stereo AAC-ljud.  
  
 Läs mer om Media Services-kodare som rör, [kodning på begäran med Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
