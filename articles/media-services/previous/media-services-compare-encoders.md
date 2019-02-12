---
title: Jämförelse av Azure på begäran media kodare | Microsoft Docs
description: Det här avsnittet jämför kodningsfunktioner av **Media Encoder Standard** och **Media Encoder Premium Workflow**.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako;anilmur
ms.openlocfilehash: 3dac469c2932276a903a516b71caa860f8870fa6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55989346"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Jämförelse av Azure på begäran media kodare  

Det här avsnittet jämför kodningsfunktioner av **Media Encoder Standard** och **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Video- och bearbetningsfunktioner

I följande tabell jämförs funktionerna mellan Media Encoder Standard (MES) och Media Encoder Premium-arbetsflöde (MEPW). 

|Funktion|Media Encoder Standard|Arbetsflöde för Media Encoder Premium|
|---|---|---|
|Tillämpa villkorsstyrd logik vid kodning<br/>(till exempel om indata är HD, sedan koda 5.1 ljud)|Nej|Ja|
|Textning|Nej|[Ja](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional Loudness Correction](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> med dialog Intelligence™|Nej|Ja|
|Avlägsnande av sammanflätning, inverterade telecine|Basic|Sändningskvalitet|
|Identifiera och ta bort svarta kantlinjer <br/>(pillarboxes letterboxes)|Nej|Ja|
|Skapa miniatyrbilder|[Ja](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ja](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Urklippet/ta bort och att gå till videor|[Ja](media-services-advanced-encoding-with-mes.md#trim_video)|Ja|
|Överlägg av ljud eller video|[Ja](media-services-advanced-encoding-with-mes.md#overlay)|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Överlägg av grafik|Från avbildningen källor|Från bild- och källor|
|Flera språkspår|Begränsad|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Fakturering mätaren som används av varje kodare
| Namn på Mediebearbetare | Gällande priser | Anteckningar |
| --- | --- | --- |
| **Media Encoder Standard** |KODARE |Kodning uppgifter kommer att debiteras utifrån den totala varaktigheten i minuter, av alla mediefiler som, priset som specificeras [här][1], under kolumnen KODARE. |
| **Arbetsflöde för Media Encoder Premium** |PREMIUM-KODARE |Kodning uppgifter kommer att debiteras utifrån den totala varaktigheten i minuter, av alla mediefiler som, priset som specificeras [här][1], under kolumnen PREMIUM-KODARE. |

## <a name="input-containerfile-formats"></a>Ange inmatningsbehållare/filformat
| Ange Inmatningsbehållare/filformat | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ja |Ja |
| MXF/SMPTE 377M |Ja |Ja |
| GXF |Ja |Ja |
| MPEG-2-transportströmmar |Ja |Ja |
| MPEG-2-strömmar |Ja |Ja |
| MPEG-4/MP4 |Ja |Ja |
| Windows Media/ASF |Ja |Ja |
| AVI (okomprimerad 8-bitars/10-bitars) |Ja |Ja |
| 3GPP/3GPP2 |Ja |Nej |
| Jämn direktuppspelning filformatet (PIFF 1.3) |Ja |Nej |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Ja |Nej |
| Matroska/WebM |Ja |Nej |
| QuickTime (.mov) |Ja |Nej |

## <a name="input-video-codecs"></a>Codec för indatavideo
| Codec för Indatavideo | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AVC 8-bitars/10-bitars, upp till 4:2:2, inklusive AVCIntra |8-bitars 4:2:0 och 4:2:2 |Ja |
| Avid DNxHD (in MXF) |Ja |Ja |
| DVCPro/DVCProHD (in MXF) |Ja |Ja |
| JPEG2000 |Ja |Ja |
| MPEG-2 (upp till 422-profil och högnivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Upp till 422-profil |Ja |
| MPEG-1 |Ja |Ja |
| Windows Media Video/VC-1 |Ja |Ja |
| Canopus HQ/HQX |Nej |Nej |
| MPEG-4, del 2 |Ja |Nej |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |Nej |
| Apple ProRes 422 |Ja |Nej |
| Apple ProRes 422 LT |Ja |Nej |
| Apple ProRes 422 HQ |Ja |Nej |
| Apple ProRes Proxy |Ja |Nej |
| Apple ProRes 4444 |Ja |Nej |
| Apple ProRes 4444 XQ |Ja |Nej |
| HEVC/H.265|Omänprofil|Huvud- och Main 10-profil|

## <a name="input-audio-codecs"></a>Indataljud
| Indataljud | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331 M och 302 M, AES3-2003) |Nej |Ja |
| Dolby® E |Nej |Ja |
| Dolby® Digital (AC3) |Nej |Ja |
| Dolby® Digital Plus (E-AC3) |Nej |Ja |
| AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1) |Ja |Ja |
| MPEG-nivå 2 |Ja |Ja |
| MP3 (MPEG-1 ljud Layer 3) |Ja |Ja |
| Windows Media Audio |Ja |Ja |
| WAV/PCM |Ja |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |Nej |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Ja |Nej |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |Nej |

## <a name="output-containerfile-formats"></a>Utdata inmatningsbehållare/filformat
| Utdata Inmatningsbehållare/filformat | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Nej |Ja |
| MXF (OP1a, XDCAM och AS02) |Nej |Ja |
| DPP (inklusive AS11) |Nej |Ja |
| GXF |Nej |Ja |
| MPEG-4/MP4 |Ja |Ja |
| MPEG-TS |Ja |Ja |
| Windows Media/ASF |Nej |Ja |
| AVI (okomprimerad 8-bitars/10-bitars) |Nej |Ja |
| Jämn direktuppspelning filformatet (PIFF 1.3) |Nej |Ja |

## <a name="output-video-codecs"></a>Codec för indatavideo-utdata
| Codec för Indatavideo-utdata | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AVC (H.264; 8-bitars, upp till hög profil nivå 5.2; 4 K Ultra HD; AVC Intra) |Endast 8-bitars 4:2:0 |Ja |
| – HEVC (H.265; 8-bitars och 10-bitars)  |Nej |Ja |
| Avid DNxHD (in MXF) |Nej |Ja |
| MPEG-2 (upp till 422-profil och högnivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Nej |Ja |
| MPEG-1 |Nej |Ja |
| Windows Media Video/VC-1 |Nej |Ja |
| JPEG-miniatyrgenerering |Ja |Ja |
| PNG miniatyrgenerering |Ja |Ja |
| BMP miniatyrgenerering |Ja |Nej |

## <a name="output-audio-codecs"></a>Utdata ljud-codec
| Utdata ljud-codec | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331 M och 302 M, AES3-2003) |Nej |Ja |
| Dolby® Digital (AC3) |Nej |Ja |
| Dolby® Digital Plus (E-AC3) upp till 7.1 |Nej |Ja |
| AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1) |Ja |Ja |
| MPEG-nivå 2 |Nej |Ja |
| MP3 (MPEG-1 ljud Layer 3) |Nej |Ja |
| Windows Media Audio |Nej |Ja |

>[!NOTE]
>Om du kodar Dolby® Digital (AC3), går det bara att skriva utdata i en ISO MP4-fil.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utför avancerad kodningsuppgifter genom att anpassa förinställningar för Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
