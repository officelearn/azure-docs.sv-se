---
title: Media Encoder Standard format och codec-Azure
description: Den här artikeln ger en översikt över Media Encoder Standard format och codecs.
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
ms.openlocfilehash: f34e3a3d1e0245180ce923fc3ec7353ab1a176be
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086361"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Standardformat för Media Encoder och codec-rutiner

> [!div class="op_single_selector" title1="Välj den version av Media Services som du använder:"]
> * [Version 2](media-services-media-encoder-standard-formats.md)
> * [Version 3](../latest/media-encoder-standard-formats.md)

Det här dokumentet innehåller en lista över de vanligaste import-och export fil formaten som du kan använda med Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Indatafil/fil format
| Filformat (filnamnstillägg) | Stöds |
| --- | --- |
| FLV (med H.264- och AAC-codec) (.flv) |Yes |
| MXF    (.mxf) |Yes |
| GXF    (.gxf) |Yes |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Yes |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Yes |
| AVI (okomprimerad 8-bitars/10-bitars) (.avi) |Yes |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Yes |
| [Microsoft Digital Video Recording(DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Yes |
| Matroska/WebM (.mkv) |Yes |
| WAVE/WAV (.wav) |Yes |
| QuickTime (.mov) |Yes |

> [!NOTE]
> Ovan är en lista över de vanligaste filnamnstilläggen. Media Encoder Standard stöder många andra (till exempel:. M2TS,. mpeg2video,. qt). Om du försöker koda en fil och får ett fel meddelande om formatet som inte stöds, kan du ange din feedback [här](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Ljud format i indata-behållare
Media Encoder Standard stöder följande ljud format i indata behållare:

* MXF-, GXF-och QuickTime-filer, som har ljud spår med överlagrade stereo-eller 5,1-exempel

eller

* MXF-, GXF- och QuickTime-filer där ljudet är separata PCM-spår, men kanalmappningen (till stereo eller 5.1) kan härledas från filens metadata

## <a name="input-video-codecs"></a>Codec för inspelning av video
| Codec för inspelning av video | Stöds |
| --- | --- |
| AVC 8-/10-bitars, upp till 4:2:2, inklusive AVCIntra |8-bitars 4:2:0 och 4:2:2 |
| Avid DNxHD (i MXF) |Yes |
| DVCPro/DVCProHD (i MXF) |Yes |
| Digital video (DV) (i AVI-filer) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (upp till 422-profil och hög nivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Upp till 422-profil |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus HQ/HQX |No |
| MPEG-4, del 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| YUV420, okomprimerad eller mezzanin |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 HQ |Yes |
| Apple ProRes Proxy |Yes |
| Apple ProRes 4444 |Yes |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H. 265| Main-och main 10-profiler (&#42;)<br/>Huvud support för 10 profiler är avsett för 8bit 4:2:0-innehåll. |

## <a name="input-audio-codecs"></a>Inmatade ljud-codec
| Codec för indataljud | Stöds |
| --- | --- |
| AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1) |Yes |
| MPEG Layer 2 |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Yes |
| Windows Media Audio |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (Adaptive Multi-Rate) |Yes |
| AES (SMPTE 331M och 302M, AES3-2003) |No |
| Dolby® E |No |
| Dolby® Digital (AC3) |No |
| Dolby® Digital Plus (E-AC3) |No |

## <a name="output-formats-and-codecs"></a>Utdataformat och codec
I följande tabell visas de codecenheter och fil format som stöds för export.

| Filformat | Video-codec | Ljud-codec |
| --- | --- | --- |
| MP4 <br/><br/>(inklusive MP4-behållare med flera bit hastigheter) |H. 264 (hög, huvud och bas linje profil) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2 – TS |H. 264 (hög, huvud och bas linje profil) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Kodning av innehåll på begäran med Azure Media Services](media-services-encode-asset.md)

[Så här kodar du med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)
