---
title: Jämförelse av media-kodare för Azure på begäran | Microsoft Docs
description: I det här avsnittet jämförs kodnings funktionerna i **Media Encoder Standard** och **Media Encoder Premium Workflow**.
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
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f42361df438a434548b3bc9394c007ef8d4c6eb0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038978"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Jämförelse av media-kodare för Azure på begäran  

I det här avsnittet jämförs kodnings funktionerna i **Media Encoder Standard** och **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Funktioner för video och ljud bearbetning

I följande tabell jämförs funktionerna mellan Media Encoder Standard (status) och Media Encoder Premium Workflow (MEPW). 

|Funktion|Media Encoder Standard|Arbetsflöde för Media Encoder Premium|
|---|---|---|
|Använd villkorsstyrd logik vid kodning<br/>(om indata till exempel är HD, koda 5,1-ljud)|Inga|Yes|
|Textning|No|[Ja](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Loudness-korrigering för Dolby &reg; Professional](https://professional.dolby.com/product/broadcast/vm600/)<br/> med dialog intelligens&trade;|Inga|Yes|
|De-sammanflätning, inverse telecine|Basic|Sändnings kvalitet|
|Identifiera och ta bort svarta kant linjer <br/>(pillarboxes, letterboxes)|Inga|Yes|
|Skapa miniatyr|[Ja](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ja](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Urklipp/trimning och häftning av videor|[Ja](media-services-advanced-encoding-with-mes.md#trim_video)|Yes|
|Överlägg med ljud eller video|[Ja](media-services-advanced-encoding-with-mes.md#overlay)|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Överlägg med bilder|Från avbildnings källor|Från bild-och text källor|
|Flera ljud språks spår|Begränsad|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Fakturerings mätare som används av varje kodare
| Medie processor namn | Tillämplig prissättning | Kommentarer |
| --- | --- | --- |
| **Media Encoder Standard** |ENCODER |Kodning av aktiviteter debiteras baserat på den totala varaktigheten, i minuter, för alla mediafiler som skapas som utdata, enligt den hastighet som anges [här][1], under kolumnen kodare. |
| **Arbetsflöde för Media Encoder Premium** |PREMIUM-KODARE |Kodning av aktiviteter debiteras baserat på den totala varaktigheten, i minuter, för alla mediafiler som skapas som utdata, enligt den hastighet som anges [här][1], under kolumnen Premium Encoder. |

## <a name="input-containerfile-formats"></a>Indatafil/fil format
| Inmatningscontainer/filformat | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| Adobe &reg; Flash- &reg; F4V |Ja |Ja |
| MXF/SMPTE 377M |Ja |Ja |
| GXF |Ja |Ja |
| MPEG-2-överförings strömmar |Ja |Ja |
| Program strömmar i MPEG-2 |Ja |Ja |
| MPEG-4/MP4 |Ja |Ja |
| Windows Media/ASF |Ja |Ja |
| AVI (okomprimerad 8bit/10bit) |Ja |Ja |
| 3GPP/3GPP2 |Yes |Inga |
| Smooth Streaming fil format (PIFF 1,3) |Yes |Inga |
| [Microsoft Digital Video inspelning (DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) |Yes |Inga |
| Matroska/WebM |Yes |Inga |
| QuickTime (.mov) |Yes |Inga |

## <a name="input-video-codecs"></a>Codec för inspelning av video
| Codec för indatavideo | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AVC 8-/10-bitars, upp till 4:2:2, inklusive AVCIntra |8-bitars 4:2:0 och 4:2:2 |Yes |
| Avid DNxHD (i MXF) |Ja |Ja |
| DVCPro/DVCProHD (i MXF) |Ja |Ja |
| JPEG2000 |Ja |Ja |
| MPEG-2 (upp till 422 profil och hög nivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs &reg; och D10) |Upp till 422-profil |Yes |
| MPEG-1 |Ja |Ja |
| Windows Media Video/VC-1 |Ja |Ja |
| Canopus HQ/HQX |Inga |Inga |
| MPEG-4, del 2 |Yes |No |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |No |
| Apple ProRes 422 |Yes |No |
| Apple ProRes 422 LT |Yes |No |
| Apple ProRes 422 HQ |Yes |No |
| Apple ProRes Proxy |Yes |No |
| Apple ProRes 4444 |Yes |No |
| Apple ProRes 4444 XQ |Yes |No |
| HEVC/H. 265|Huvud profil|Main-och main 10-profil|

## <a name="input-audio-codecs"></a>Inmatade ljud-codec
| Codec för indataljud | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M och 302M, AES3-2003) |No |Yes |
| Dolby &reg; E |No |Yes |
| Dolby &reg; digital (AC3) |No |Yes |
| Dolby &reg; Digital Plus (E-AC3) |No |Yes |
| AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1) |Ja |Ja |
| MPEG Layer 2 |Ja |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Ja |Ja |
| Windows Media Audio |Ja |Ja |
| WAV/PCM |Ja |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |No |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Yes |No |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |No |

## <a name="output-containerfile-formats"></a>Utmatnings behållare/fil format
| Utmatnings behållare/fil format | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| Adobe &reg; Flash- &reg; F4V |No |Yes |
| MXF (OP1a, XDCAM och AS02) |No |Yes |
| DPP (inklusive AS11) |Inga |Yes |
| GXF |Inga |Yes |
| MPEG-4/MP4 |Ja |Ja |
| MPEG – TS |Ja |Ja |
| Windows Media/ASF |Inga |Yes |
| AVI (okomprimerad 8bit/10bit) |Inga |Yes |
| Smooth Streaming fil format (PIFF 1,3) |Inga |Yes |

## <a name="output-video-codecs"></a>Codec för video utgång
| Codec för video utgång | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AVC (H. 264; 8-bitars, upp till hög profil, nivå 5,2; 4K Ultra HD; AVC Intra) |Endast 8-bitars 4:2:0 |Yes |
| HEVC (H. 265; 8-bitars och 10-bitars;)  |Inga |Yes |
| Avid DNxHD (i MXF) |Inga |Yes |
| MPEG-2 (upp till 422 profil och hög nivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs &reg; och D10) |Inga |Yes |
| MPEG-1 |Inga |Yes |
| Windows Media Video/VC-1 |Inga |Yes |
| Skapa JPEG-miniatyr |Ja |Ja |
| Skapa miniatyr av PNG |Ja |Ja |
| BMP-miniatyren skapas |Yes |Inga |

## <a name="output-audio-codecs"></a>Ljud-codec för utdata
| Ljud-codec för utdata | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M och 302M, AES3-2003) |Inga |Yes |
| Dolby &reg; digital (AC3) |Inga |Yes |
| Dolby &reg; Digital Plus (E-AC3) upp till 7,1 |Inga |Yes |
| AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1) |Ja |Ja |
| MPEG Layer 2 |Inga |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Inga |Yes |
| Windows Media Audio |Inga |Yes |

>[!NOTE]
>Om du kodar till Dolby &reg; digital (AC3) kan utdata bara skrivas till en ISO MP4-fil.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodnings uppgifter genom att anpassa Media Encoder Standard för inställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
