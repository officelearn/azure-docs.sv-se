---
title: Uppgift förinställningar för Media Encoder Standard (MES) | Microsoft Docs
description: Avsnittet ger och översikt över service-definierade exempel förinställningar för Media Encoder Standard (MES).
author: Juliako
manager: cfow
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: juliako
ms.openlocfilehash: 4c518771d95dcb571da294f8a752e7b5740ef19f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Exempel förinställningar för Media Encoder Standard (MES)

**Media Encoder Standard** definierar en uppsättning fördefinierade system kodning förinställningar som du kan använda när du skapar kodning jobb. Det rekommenderas att använda ”anpassningsbar strömning” förinställningen om du vill koda en video för strömning med Media Services. När du anger detta förinställda, Media Encoder Standard tar [Autogenerera en bithastighet stege](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Skapa anpassade förinställningar prover
Media Services stöder anpassning av alla värden i förinställningar för att uppfylla dina kodning behov och krav. Om du behöver anpassa en kodning förinställning ska du starta med en av de nedan systeminställningar som har angetts i det här avsnittet som en mall för en anpassad konfiguration. Förklaringar av vad varje element i dessa förinställda innebär och giltiga värden för varje element finns i [Media Encoder Standard schemat](media-services-mes-schema.md) avsnittet.  
  
> [!NOTE]
>  När du använder en förinställning för 4k kodar, ska du hämta den `S3` reserverade enhetstyp. Mer information finns i [så skala kodning](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Rotera videoklippet standardinställningen i förinställningar:
När du arbetar med Media Encoder Standard är video rotation aktiverad som standard. Om videon har registrerats på en mobil enhet i stående läge, sedan roteras dessa förinställningar dem till liggande läge före kodning.
 
## <a name="available-presets"></a>Tillgängliga förinställningar: 

 [H264 Multibithastighet 1080p ljud 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) producerar en uppsättning 8 GOP-justerad MP4-filer, allt från 6000 kbit/s till 400 kbit/s och AAC 5.1 ljud.  
  
 [H264 Multibithastighet 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) producerar en uppsättning 8 GOP-justerad MP4-filer, allt från 6000 kbit/s till 400 kbit/s och AAC stereoljud.  
  
 [H264 Multibithastighet 16 x 9 för iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) producerar en uppsättning 8 GOP-justerad MP4-filer, allt från 8500 kbit/s till 200 kbit/s och AAC stereoljud.  
  
 [H264 Multibithastighet 16 x 9 SD ljud 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) producerar en uppsättning 5 GOP-justerad MP4-filer, allt från 1900 kbit/s till 400 kbit/s och AAC 5.1 ljud.  
  
 [H264 Multibithastighet 16 x 9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) producerar en uppsättning 5 GOP-justerad MP4-filer, allt från 1900 kbit/s till 400 kbit/s och AAC stereoljud.  
  
 [H264 Multibithastighet 4K ljud 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) producerar en uppsättning 12 GOP-justerad MP4-filer, mellan 20000 kbit/s och 1 000 kbit/s och AAC 5.1 ljud.  
  
 [H264 Multibithastighet 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) producerar en uppsättning 12 GOP-justerad MP4-filer, mellan 20000 kbit/s och 1 000 kbit/s och AAC stereoljud.  
  
 [H264 Multibithastighet 4 x 3 för iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) producerar en uppsättning 8 GOP-justerad MP4-filer, allt från 8500 kbit/s till 200 kbit/s och AAC stereoljud.  
  
 [H264 Multibithastighet 4 x 3 SD ljud 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) producerar en uppsättning 5 GOP-justerad MP4-filer, allt från 1600 kbit/s till 400 kbit/s och AAC 5.1 ljud.  
  
 [H264 Multibithastighet 4 x 3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) producerar en uppsättning 5 GOP-justerad MP4-filer, allt från 1600 kbit/s till 400 kbit/s och AAC stereoljud.  
  
 [H264 Multibithastighet 720p ljud 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) producerar en uppsättning 6 GOP-justerad MP4-filer, allt från 3400 kbit/s till 400 kbit/s och AAC 5.1 ljud.  
  
 [H264 Multibithastighet 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) producerar en uppsättning 6 GOP-justerad MP4-filer, allt från 3400 kbit/s till 400 kbit/s och AAC stereoljud.  
  
 [H264 enkel bithastighet 1080p ljud 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) producerar en enda MP4-fil med en bithastighet 6750 kbit/s och AAC 5.1 ljud.  
  
 [H264 enkel bithastighet 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) producerar en enda MP4-fil med en bithastighet 6750 kbit/s och AAC stereoljud.  
  
 [H264 enkel bithastighet 4K ljud 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) producerar en enda MP4-fil med en bithastighet 18000 kbit/s och AAC 5.1 ljud.  
  
 [H264 enkel bithastighet 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) producerar en enda MP4-fil med en bithastighet 18000 kbit/s och AAC stereoljud.  
  
 [H264 enkel bithastighet 4 x 3 SD ljud 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) producerar en enda MP4-fil med en bithastighet 1800 kbit/s och AAC 5.1 ljud.  
  
 [H264 enkel bithastighet 4 x 3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) producerar en enda MP4-fil med en bithastighet 1800 kbit/s och AAC stereoljud.  
  
 [H264 enkel bithastighet 16 x 9 SD ljud 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) producerar en enda MP4-fil med en bithastighet 2200 kbit/s och AAC 5.1 ljud.  
  
 [H264 enkel bithastighet 16 x 9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) producerar en enda MP4-fil med en bithastighet 2200 kbit/s och AAC stereoljud.  
  
 [H264 enkel bithastighet 720p ljud 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) producerar en enda MP4-fil med en bithastighet 4500 kbit/s och AAC 5.1 ljud.  
  
 [H264 enkel bithastighet 720p för Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) förinställda producerar en enda MP4-fil med en bithastighet 2000 kbit/s och stereo AAC.  
  
 [H264 enkel bithastighet 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) producerar en enda MP4-fil med en bithastighet 4500 kbit/s och AAC stereoljud.  
  
 [H264 enkel bithastighet hög kvalitet SD för Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) producerar en enda MP4-fil med en bithastighet 500 kbit/s och AAC stereoljud...  
  
 [H264 enkel bithastighet låg kvalitet SD för Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) producerar en enda MP4-fil med en bithastighet 56 kbit/s och AAC stereoljud.  
  
 Mer information som rör Media Services kodare finns [kodning på begäran med Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
