---
title: Aktivitetsförinställningar för Media Encoder Standard (MES) | Microsoft-dokument
description: Avsnittet innehåller och översikt över de tjänstdefinierade exempelförinställningarna för MEDIA Encoder Standard (MES).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463411"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Exempel förinställningar för Media Encoder Standard (MES)

**Media Encoder Standard** definierar en uppsättning fördefinierade systemkodningsförinställningar som du kan använda när du skapar kodningsjobb. Vi rekommenderar att du använder förinställningen "Adaptive Streaming" om du vill koda en video för direktuppspelning med Media Services. När du anger den här förinställningen genererar Media Encoder Standard [automatiskt en bithastighetsstege](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Skapa anpassade förinställningar från exempel
Media Services stöder fullt ut att anpassa alla värden i förinställningar för att uppfylla dina specifika kodningsbehov och krav. Om du behöver anpassa en kodningsförinställning bör du börja med en av nedanstående systemförinställningar som finns i det här avsnittet som en mall för din anpassade konfiguration. Förklaringar av vad varje element i dessa förinställningar innebär och giltiga värden för varje element finns i [schemaavsnittet Media Encoder Standard.](media-services-mes-schema.md)  
  
> [!NOTE]
>  När du använder en förinställning för 4k-kodningar bör du hämta den reserverade enhetstypen. `S3` Mer information finns i [Så här skalar du kodning](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Standardinställning för videorotation i förinställningar:
När du arbetar med Media Encoder Standard aktiveras videorotation som standard. Om videon har spelats in på en mobil enhet i stående läge roterar dessa förinställningar dem till liggande läge före kodningen.
 
## <a name="available-presets"></a>Tillgängliga förinställningar: 

 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) ger en uppsättning 8 GOP-anpassade MP4-filer, från 6000 kbps till 400 kbps och AAC 5.1-ljud.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) producerar en uppsättning 8 GOP-anpassade MP4-filer, från 6000 kbps till 400 kbps och stereo AAC-ljud.  
  
 [H264 Multiple Bitrate 16x9 för iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) ger en uppsättning 8 GOP-anpassade MP4-filer, från 8500 kbps till 200 kbps och stereo AAC-ljud.  
  
 [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) ger en uppsättning 5 GOP-anpassade MP4-filer, från 1900 kbps till 400 kbps och AAC 5.1-ljud.  
  
 [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) producerar en uppsättning 5 GOP-anpassade MP4-filer, från 1900 kbps till 400 kbps och stereo AAC-ljud.  
  
 [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) ger en uppsättning 12 GOP-anpassade MP4-filer, från 20000 kbps till 1000 kbps och AAC 5.1-ljud.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) producerar en uppsättning 12 GOP-anpassade MP4-filer, från 20000 kbps till 1000 kbps och stereo AAC-ljud.  
  
 [H264 Multiple Bitrate 4x3 för iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) ger en uppsättning 8 GOP-anpassade MP4-filer, från 8500 kbps till 200 kbps och stereo AAC-ljud.  
  
 [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) ger en uppsättning 5 GOP-anpassade MP4-filer, från 1600 kbps till 400 kbps och AAC 5.1-ljud.  
  
 [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) producerar en uppsättning 5 GOP-anpassade MP4-filer, från 1600 kbps till 400 kbps och stereo AAC-ljud.  
  
 [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) ger en uppsättning 6 GOP-anpassade MP4-filer, från 3400 kbps till 400 kbps och AAC 5.1-ljud.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) producerar en uppsättning 6 GOP-anpassade MP4-filer, från 3400 kbps till 400 kbps och stereo AAC-ljud.  
  
 [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) ger en enda MP4-fil med en bithastighet på 6750 kbps och AAC 5.1-ljud.  
  
 [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) producerar en enda MP4-fil med en bithastighet på 6750 kbps och stereo AAC-ljud.  
  
 [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) ger en enda MP4-fil med en bithastighet på 18000 kbps och AAC 5.1-ljud.  
  
 [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) producerar en enda MP4-fil med en bithastighet på 18000 kbps och stereo AAC-ljud.  
  
 [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) ger en enda MP4-fil med en bithastighet på 1800 kbps och AAC 5.1-ljud.  
  
 [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) producerar en enda MP4-fil med en bithastighet på 1800 kbps och stereo AAC-ljud.  
  
 [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) ger en enda MP4-fil med en bithastighet på 2200 kbps och AAC 5.1-ljud.  
  
 [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) producerar en enda MP4-fil med en bithastighet på 2200 kbps och stereo AAC-ljud.  
  
 [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) ger en enda MP4-fil med en bithastighet på 4500 kbps och AAC 5.1-ljud.  
  
 [H264 Single Bitrate 720p för Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) förinställda ger en enda MP4-fil med en bithastighet på 2000 kbps och stereo AAC.  
  
 [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) producerar en enda MP4-fil med en bithastighet på 4500 kbps och stereo AAC-ljud.  
  
 [H264 Single Bitrate Hög kvalitet SD för Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) producerar en enda MP4-fil med en bithastighet på 500 kbps, och stereo AAC ljud..  
  
 [H264 Single Bitrate Låg kvalitet SD för Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) producerar en enda MP4-fil med en bithastighet på 56 kbps, och stereo AAC ljud.  
  
 Mer information om Media Services-kodare finns i [Kodning på begäran med Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
