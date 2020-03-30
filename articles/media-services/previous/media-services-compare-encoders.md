---
title: Jämförelse av Azure on demand media-kodare | Microsoft-dokument
description: I det här avsnittet jämförs kodningsfunktionerna **i Media Encoder Standard** och Media **Encoder Premium Workflow**.
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
ms.openlocfilehash: 4767f7bb5ba02c838c0e21721e55a6564a14acd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016661"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Jämförelse av Azure on demand media-kodare  

I det här avsnittet jämförs kodningsfunktionerna **i Media Encoder Standard** och Media **Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Funktioner för video- och ljudbearbetning

I följande tabell jämförs funktionerna mellan MES (Media Encoder Standard) och Media Encoder Premium Workflow (MEPW). 

|Funktion|Media Encoder Standard|Arbetsflöde för Media Encoder Premium|
|---|---|---|
|Använda villkorsstyrd logik vid kodning<br/>(till exempel om ingången är HD, sedan koda 5,1 ljud)|Inga|Ja|
|Textning|Inga|[Ja](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professionell Loudness Korrigering](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> med Dialog Intelligence™|Inga|Ja|
|Avförstörande, omvänd telecine|Basic|Sändningskvalitet|
|Identifiera och ta bort svarta kantlinjer <br/>(pelarlådor, brevlådor)|Inga|Ja|
|Miniatyrgenerering|[Ja](media-services-dotnet-generate-thumbnail-with-mes.md)|[Ja](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Klippning/trimning och sömnad av videor|[Ja](media-services-advanced-encoding-with-mes.md#trim_video)|Ja|
|Överlägg av ljud eller video|[Ja](media-services-advanced-encoding-with-mes.md#overlay)|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Överlägg av grafik|Från bildkällor|Från bild- och textkällor|
|Flera ljudspråkspår|Begränsad|[Ja](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Faktureringsmätare som används av varje kodare
| Namn på medieprocessor | Tillämplig prissättning | Anteckningar |
| --- | --- | --- |
| **Media Encoder Standard** |Kodare |Kodning uppgifter kommer att debiteras baserat på den totala varaktigheten, i minuter, av alla mediefiler som produceras som utdata, med den hastighet som anges [här][1], under kolumnen KODARE. |
| **Arbetsflöde för Media Encoder Premium** |PREMIUM KODARE |Kodning uppgifter kommer att debiteras baserat på den totala varaktigheten, i minuter, av alla mediefiler som produceras som utdata, med den hastighet som anges [här][1], under kolumnen PREMIUM ENCODER. |

## <a name="input-containerfile-formats"></a>Inmatningsbehållare/filformat
| Inmatningscontainer/filformat | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Ja |Ja |
| MXF/SMPTE 377M |Ja |Ja |
| GXF (på andra sätt) |Ja |Ja |
| MPEG-2 Transportströmmar |Ja |Ja |
| MPEG-2 Program Strömmar |Ja |Ja |
| MPEG-4/MP4 |Ja |Ja |
| Windows Media/ASF |Ja |Ja |
| AVI (Okomprimerad 8bit/10bit) |Ja |Ja |
| 3GPP/3GPP2 |Ja |Inga |
| Jämnt direktuppspelningsfilformat (PIFF 1.3) |Ja |Inga |
| [Microsoft Digital videoinspelning(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Ja |Inga |
| Matroska/WebM |Ja |Inga |
| QuickTime (.mov) |Ja |Inga |

## <a name="input-video-codecs"></a>Indata video codec
| Codec för indatavideo | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AVC 8-/10-bitars, upp till 4:2:2, inklusive AVCIntra |8-bitars 4:2:0 och 4:2:2 |Ja |
| Avid DNxHD (i MXF) |Ja |Ja |
| DVCPro/DVCProHD (i MXF) |Ja |Ja |
| JPEG2000 |Ja |Ja |
| MPEG-2 (upp till 422 profil och hög nivå; inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Upp till 422-profil |Ja |
| MPEG-1 |Ja |Ja |
| Windows Media Video/VC-1 |Ja |Ja |
| Canopus HQ/HQX |Inga |Inga |
| MPEG-4, del 2 |Ja |Inga |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |Inga |
| Apple ProRes 422 |Ja |Inga |
| Apple ProRes 422 LT |Ja |Inga |
| Apple ProRes 422 HQ |Ja |Inga |
| Apple ProRes Proxy |Ja |Inga |
| Apple ProRes 4444 |Ja |Inga |
| Apple ProRes 4444 XQ |Ja |Inga |
| HEVC/H.265|Huvudprofil|Huvud- och huvud10-profil|

## <a name="input-audio-codecs"></a>Ingående ljudcodec
| Codec för indataljud | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M och 302M, AES3-2003) |Inga |Ja |
| Dolby® E |Inga |Ja |
| Dolby® Digital (AC3) |Inga |Ja |
| Dolby® Digital Plus (E-AC3) |Inga |Ja |
| AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1) |Ja |Ja |
| MPEG Layer 2 |Ja |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Ja |Ja |
| Windows Media Audio |Ja |Ja |
| WAV/PCM |Ja |Ja |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |Inga |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Ja |Inga |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |Inga |

## <a name="output-containerfile-formats"></a>Utdatabehållare/filformat
| Utdatabehållare/filformat | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| Adobe® Flash® F4V |Inga |Ja |
| MXF (OP1a, XDCAM och AS02) |Inga |Ja |
| DPP (inklusive AS11) |Inga |Ja |
| GXF (på andra sätt) |Inga |Ja |
| MPEG-4/MP4 |Ja |Ja |
| MPEG-TS |Ja |Ja |
| Windows Media/ASF |Inga |Ja |
| AVI (Okomprimerad 8bit/10bit) |Inga |Ja |
| Jämnt direktuppspelningsfilformat (PIFF 1.3) |Inga |Ja |

## <a name="output-video-codecs"></a>Utdatavideocodec
| Utdata video codec | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AVC (H.264; 8-bitars; upp till hög profil, nivå 5.2; 4K Ultra HD; AVC Intra) |Endast 8 bitars 4:2:0 |Ja |
| HEVC (H.265; 8-bitars och 10-bitars;)  |Inga |Ja |
| Avid DNxHD (i MXF) |Inga |Ja |
| MPEG-2 (upp till 422 profil och hög nivå; inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Inga |Ja |
| MPEG-1 |Inga |Ja |
| Windows Media Video/VC-1 |Inga |Ja |
| Skapa JPEG-miniatyrer |Ja |Ja |
| PNG-miniatyrbild skapas |Ja |Ja |
| BMP-miniatyrbild skapas |Ja |Inga |

## <a name="output-audio-codecs"></a>Utdata ljud codec
| Utdata ljud codec | Media Encoder Standard | Arbetsflöde för Media Encoder Premium |
| --- | --- | --- |
| AES (SMPTE 331M och 302M, AES3-2003) |Inga |Ja |
| Dolby® Digital (AC3) |Inga |Ja |
| Dolby® Digital Plus (E-AC3) upp till 7,1 |Inga |Ja |
| AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1) |Ja |Ja |
| MPEG Layer 2 |Inga |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Inga |Ja |
| Windows Media Audio |Inga |Ja |

>[!NOTE]
>Om du kodar till Dolby® Digital (AC3) kan utdata bara skrivas in i en ISO MP4-fil.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodningsuppgifter genom att anpassa standardförinställningar för mediekodare](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
