---
title: Mediekodare Standardformat och codec - Azure
description: Den här artikeln innehåller en översikt över mediekodares standardformat och codec-enheter.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: e2ccce13e4ef09426d0f3a02dcbce2f330b0ead8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251029"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Standardformat för Media Encoder och codec-rutiner

> [!div class="op_single_selector" title1="Välj den version av Media Services som du använder:"]
> * [Version 2](media-services-media-encoder-standard-formats.md)
> * [Version 3](../latest/media-encoder-standard-formats.md)

Det här dokumentet innehåller en lista över de vanligaste import- och exportfilformaten som du kan använda med Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Inmatningsbehållare/filformat
| Filformat (filnamnstillägg) | Stöds |
| --- | --- |
| FLV (med H.264- och AAC-codec) (.flv) |Ja |
| MXF    (.mxf) |Ja |
| GXF    (.gxf) |Ja |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Ja |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Ja |
| AVI (okomprimerad 8-bitars/10-bitars) (.avi) |Ja |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Ja |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Ja |
| Matroska/WebM (.mkv) |Ja |
| WAVE/WAV (.wav) |Ja |
| QuickTime (.mov) |Ja |

> [!NOTE]
> Ovan är en lista över de vanligaste filnamnstilläggen. Media Encoder Standard stöder många andra (till exempel: .m2ts, .mpeg2video, .qt). Om du försöker koda en fil och får ett felmeddelande om att formatet inte stöds kan du ge din feedback [här](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Ljudformat i inmatningsbehållare
Media Encoder Standard stöder att bära följande ljudformat i inmatningsbehållare:

* MXF-, GXF- och QuickTime-filer, som har ljudspår med interfolierad stereo eller 5,1-prover

eller

* MXF-, GXF- och QuickTime-filer där ljudet är separata PCM-spår, men kanalmappningen (till stereo eller 5.1) kan härledas från filens metadata

## <a name="input-video-codecs"></a>Indata video codec
| Indata video codec | Stöds |
| --- | --- |
| AVC 8-/10-bitars, upp till 4:2:2, inklusive AVCIntra |8-bitars 4:2:0 och 4:2:2 |
| Avid DNxHD (i MXF) |Ja |
| DVCPro/DVCProHD (i MXF) |Ja |
| Digital video (DV) (i AVI-filer) |Ja |
| JPEG 2000 |Ja |
| MPEG-2 (upp till 422-profil och hög nivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Upp till 422-profil |
| MPEG-1 |Ja |
| VC-1/WMV9 |Ja |
| Canopus HQ/HQX |Inga |
| MPEG-4, del 2 |Ja |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |
| YUV420, okomprimerad eller mezzanin |Ja |
| Apple ProRes 422 |Ja |
| Apple ProRes 422 LT |Ja |
| Apple ProRes 422 HQ |Ja |
| Apple ProRes Proxy |Ja |
| Apple ProRes 4444 |Ja |
| Apple ProRes 4444 XQ |Ja |
| HEVC/H.265| Huvud- och huvudprofiler (&#42;)<br/>Support för huvud 10-profil är avsedd för 8bit 4:2:0-innehåll. |

## <a name="input-audio-codecs"></a>Ingående ljudcodec
| Codec för indataljud | Stöds |
| --- | --- |
| AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1) |Ja |
| MPEG Layer 2 |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Ja |
| Windows Media Audio |Ja |
| WAV/PCM |Ja |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Ja |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |
| AMR (Adaptive Multi-Rate) |Ja |
| AES (SMPTE 331M och 302M, AES3-2003) |Inga |
| Dolby® E |Inga |
| Dolby® Digital (AC3) |Inga |
| Dolby® Digital Plus (E-AC3) |Inga |

## <a name="output-formats-and-codecs"></a>Utdataformat och codec-enheter
I följande tabell visas de codec-enheter och filformat som stöds för export.

| Filformat | Video Codec | Ljud Codec |
| --- | --- | --- |
| MP4 (mp4) <br/><br/>(inklusive MP4-behållare med flera bithastigheter) |H.264 (hög-, huvud- och baslinjeprofiler) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (hög-, huvud- och baslinjeprofiler) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Koda innehåll på begäran med Azure Media Services](media-services-encode-asset.md)

[Så här kodar du med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

