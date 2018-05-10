---
title: Jämförelse mellan Azure på begäran mediet kodare | Microsoft Docs
description: Det här avsnittet jämför kodning funktionerna i **Media Encoder Standard** och **Media Encoder Premium arbetsflöde**.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 13e8b3bcb7de4093116b3e2198b210950ac16d78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Jämförelse mellan Azure på begäran mediet kodare

Det här avsnittet jämför kodning funktionerna i **Media Encoder Standard** och **Media Encoder Premium arbetsflöde**.

## <a name="video-and-audio-processing-capabilities"></a>Video och ljud bearbetningsfunktioner

I följande tabell jämförs funktionerna mellan Media Encoder Standard (MES) och Media Encoder Premium arbetsflöde (MEPW). 

|Funktion|Media Encoder Standard|Arbetsflöde för Media Encoder Premium|
|---|---|---|
|Tillämpa villkorlig logik vid kodning<br/>(till exempel om indata är HD, sedan koda 5.1 ljud)|Nej|Ja|
|Dold textning|Nej|[Ja](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional Loudness korrigering](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> med dialog Intelligence™|Nej|Ja|
|De-interlacing, inverterade telecine|Basic|Broadcast kvalitet|
|Identifiera och ta bort svart kantlinjer <br/>(pillarboxes letterboxes)|Nej|Ja|
|Miniatyrer generation|[Ja](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ja](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Urklippet/trimning och videoklipp med att gå till|[Ja](media-services-advanced-encoding-with-mes.md#trim_video)|Ja|
|Överlägg av ljud och video|[Ja](media-services-advanced-encoding-with-mes.md#overlay)|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Överlägg av grafik|Från avbildningen källor|Från bilden och texten källor|
|Spår för flera språk för ljud|Begränsad|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Fakturering mätaren som används av varje kodaren
| Namnet på mediet-Processor | Gällande priser | Anteckningar |
| --- | --- | --- |
| **Media Encoder Standard** |KODAREN |Kodning uppgifter debiteras baserat på den sammanlagda tiden i minuter, av alla mediefiler som, vid den angivna [här][1], under kolumnen KODARE. |
| **Arbetsflöde för Media Encoder Premium** |PREMIUM-KODARE |Kodning uppgifter debiteras baserat på den sammanlagda tiden i minuter, av alla mediefiler som, vid den angivna [här][1], under kolumnen PREMIUM-KODARE. |

## <a name="input-containerfile-formats"></a>Ange behållare/filformat
| Ange behållare/filformat | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ja |Ja |
| MXF/SMPTE 377M |Ja |Ja |
| GXF |Ja |Ja |
| MPEG-2-transportströmmar |Ja |Ja |
| MPEG-2-strömmar |Ja |Ja |
| MP4-MPEG-4 |Ja |Ja |
| Windows Media/ASF |Ja |Ja |
| AVI (okomprimerad 8-bitars/10 bitar) |Ja |Ja |
| 3GPP/3GPP2 |Ja |Nej |
| Filformatet för Smooth Streaming (PIFF 1.3) |Ja |Nej |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Ja |Nej |
| Matroska/WebM |Ja |Nej |
| QuickTime (.mov) |Ja |Nej |

## <a name="input-video-codecs"></a>Inkommande video-codec
| Inkommande Video-codec | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AVC 8-/ 10-bitars, upp till 4:2:2, inklusive AVCIntra |8-bitars 4:2:0 och 4:2:2 |Ja |
| Avid DNxHD (i MXF) |Ja |Ja |
| DVCPro/DVCProHD (i MXF) |Ja |Ja |
| JPEG2000 |Ja |Ja |
| MPEG-2 (upp till 422 profil och högnivå, inklusive varianter, till exempel XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Upp till 422 profil |Ja |
| MPEG-1 |Ja |Ja |
| Windows Media Video-VC-1 |Ja |Ja |
| Canopus HQ/HQX |Nej |Nej |
| MPEG-4 del 2 |Ja |Nej |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |Nej |
| Apple ProRes 422 |Ja |Nej |
| Apple ProRes 422 LT |Ja |Nej |
| Apple ProRes 422 HQ |Ja |Nej |
| Apple ProRes Proxy |Ja |Nej |
| Apple ProRes 4444 |Ja |Nej |
| Apple ProRes 4444 XQ |Ja |Nej |

## <a name="input-audio-codecs"></a>Inkommande ljud-codec
| Inkommande ljud-codec | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331 M och 302 M, AES3 2003) |Nej |Ja |
| Dolby® E |Nej |Ja |
| Dolby® Digital (AC3) |Nej |Ja |
| Dolby® Digital Plus (E-AC3) |Nej |Ja |
| AAC (AAC LC, HE AAC och AAC-HEv2 upp till 5.1) |Ja |Ja |
| MPEG nivå 2 |Ja |Ja |
| MP3 (MPEG-1 ljud nivå 3) |Ja |Ja |
| Windows Media Audio |Ja |Ja |
| WAV PCM / |Ja |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |Nej |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Ja |Nej |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |Nej |

## <a name="output-containerfile-formats"></a>Utdata behållare/filformat
| Utdata behållare/filformat | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Nej |Ja |
| MXF (OP1a, XDCAM och AS02) |Nej |Ja |
| DPP (inklusive AS11) |Nej |Ja |
| GXF |Nej |Ja |
| MP4-MPEG-4 |Ja |Ja |
| MPEG-TS |Ja |Ja |
| Windows Media/ASF |Nej |Ja |
| AVI (okomprimerad 8-bitars/10 bitar) |Nej |Ja |
| Filformatet för Smooth Streaming (PIFF 1.3) |Nej |Ja |

## <a name="output-video-codecs"></a>Utdata video-codec
| Utdata Video-codec | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AVC (H.264; 8-bitars, upp till hög profil nivå 5.2; 4 K extra HD; AVC Intra) |Endast 8-bitars 4:2:0 |Ja |
| HEVC (H.265; 8-bitars och 10-bitars)  |Nej |Ja |
| Avid DNxHD (i MXF) |Nej |Ja |
| MPEG-2 (upp till 422 profil och högnivå, inklusive varianter, till exempel XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Nej |Ja |
| MPEG-1 |Nej |Ja |
| Windows Media Video-VC-1 |Nej |Ja |
| JPEG-miniatyrer skapas |Ja |Ja |
| PNG miniatyrer skapas |Ja |Ja |
| BMP miniatyrer skapas |Ja |Nej |

## <a name="output-audio-codecs"></a>Ljud-codec utdata
| Ljud-codec utdata | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331 M och 302 M, AES3 2003) |Nej |Ja |
| Dolby® Digital (AC3) |Nej |Ja |
| Dolby® Digital Plus (E-AC3) upp till 7.1 |Nej |Ja |
| AAC (AAC LC, HE AAC och AAC-HEv2 upp till 5.1) |Ja |Ja |
| MPEG nivå 2 |Nej |Ja |
| MP3 (MPEG-1 ljud nivå 3) |Nej |Ja |
| Windows Media Audio |Nej |Ja |

>[!NOTE]
>Om du koda till Dolby® Digital (AC3) kan endast utdata skrivs till en ISO MP4-fil.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodning uppgifter genom att anpassa Media Encoder Standard förinställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
