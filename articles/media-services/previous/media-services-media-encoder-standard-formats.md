---
title: Media Encoder Standard format och -codec - Azure
description: Det här avsnittet ger en översikt över Media Encoder Standard format och -codec.
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
ms.author: juliako;anilmur
ms.openlocfilehash: c862de2eec4e6c116218457a20b567dc02778685
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190030"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Standardformat för Media Encoder och codec-rutiner

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Version 2](media-services-media-encoder-standard-formats.md)
> * [Version 3](../latest/media-encoder-standard-formats.md)

Det här dokumentet innehåller en lista med de vanligaste import- och exportfilformat som du kan använda med Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Ange inmatningsbehållare/filformat
| Filformat (filnamnstillägg) | Stöds |
| --- | --- |
| FLV (med H.264- och AAC-codec) (.flv) |Ja |
| MXF    (.mxf) |Ja |
| GXF (.gxf) |Ja |
| MPEG2-PS MPEG2-TS 3GP (TS, PS, .3gp, .3gpp, MPG) |Ja |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Ja |
| AVI (okomprimerad 8-bitars/10-bitars) (.avi) |Ja |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Ja |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Ja |
| Matroska/WebM (.mkv) |Ja |
| WAVE/WAV (.wav) |Ja |
| QuickTime (.mov) |Ja |

> [!NOTE]
> Är en lista över de vanligaste filnamnstilläggen. Media Encoder Standard stöder många andra (till exempel: .m2ts, .mpeg2video, .qt). Om du försöker koda en fil och du får ett felmeddelande om formatet inte stöds, ge feedback [här](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Ljudformat i indata-behållare
Media Encoder Standard stöder medför följande ljudformat i indata behållare:

* MXF-, GXF- och QuickTime-filer, som har ljudspår med överlagrad stereo eller 5.1-exempel

eller

* MXF-, GXF- och QuickTime-filer där ljudet utförs som separata PCM-spår, men kanalmappningen (till stereo eller 5.1) kan härledas från filens metadata

## <a name="input-video-codecs"></a>Codec för indatavideo
| Codec för indatavideo | Stöds |
| --- | --- |
| AVC 8-bitars/10-bitars, upp till 4:2:2, inklusive AVCIntra |8-bitars 4:2:0 och 4:2:2 |
| Avid DNxHD (in MXF) |Ja |
| DVCPro/DVCProHD (in MXF) |Ja |
| Digital video (DV) (i AVI-filer) |Ja |
| JPEG 2000 |Ja |
| MPEG-2 (upp till 422-profil och högnivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Upp till 422-profil |
| MPEG-1 |Ja |
| VC-1/WMV9 |Ja |
| Canopus HQ/HQX |Nej |
| MPEG-4, del 2 |Ja |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |
| Yuv420, okomprimerad eller mezzanin |Ja |
| Apple ProRes 422 |Ja |
| Apple ProRes 422 LT |Ja |
| Apple ProRes 422 HQ |Ja |
| Apple ProRes Proxy |Ja |
| Apple ProRes 4444 |Ja |
| Apple ProRes 4444 XQ |Ja |
| HEVC/H.265| Huvud- och Main 10 (&#42;) profiler<br/>Stöd för main 10-profil är avsedd för 8-bitars 4:2:0 innehåll. |

## <a name="input-audio-codecs"></a>Indataljud
| Indataljud | Stöds |
| --- | --- |
| AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1) |Ja |
| MPEG-nivå 2 |Ja |
| MP3 (MPEG-1 ljud Layer 3) |Ja |
| Windows Media Audio |Ja |
| WAV/PCM |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Ja |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |
| AMR (anpassningsbar Multi-Rate) |Ja |
| AES (SMPTE 331 M och 302 M, AES3-2003) |Nej |
| Dolby® E |Nej |
| Dolby® Digital (AC3) |Nej |
| Dolby® Digital Plus (E-AC3) |Nej |

## <a name="output-formats-and-codecs"></a>Utdataformat och -codec
I följande tabell visas de codec- och filformat som stöds för export.

| Filformat | Video-Codec | Ljudcodec |
| --- | --- | --- |
| MP4 <br/><br/>(inklusive med flera bithastigheter MP4-behållare) |H.264 (hög, Main och baslinje-profiler) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (hög, Main och baslinje-profiler) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se också
[Koda innehåll på begäran med Azure Media Services](media-services-encode-asset.md)

[Koda med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

