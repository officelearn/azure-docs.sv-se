---
title: Media Encoder Standard format och codec
description: Det här avsnittet ger en översikt över Media Encoder Standard format och codec.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: f334b1ce-2f56-4968-a019-f0a2b0016d9f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 181a1b8ad6403045264ddc0bd502273f36df3eff
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638338"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Standardformat för Media Encoder och codec-rutiner
Det här dokumentet innehåller en lista över de vanligaste import och export-filformat som du kan använda med Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Ange behållare/filformat
| Filformat (filnamnstillägg) | Stöds |
| --- | --- | --- | --- |
| FLV (med H.264 och AAC codec) (.flv) |Ja |
| MXF (.mxf) |Ja |
| GXF (.gxf) |Ja |
| MPEG2-PS, MPEG2 TS 3GP (.ts, PS, .3gp, .3gpp, MPG) |Ja |
| Windows Media Video (WMV) / ASF (.wmv, .asf) |Ja |
| AVI (okomprimerad 8-bitars/10 bitar) (.avi) |Ja |
| MP4 (.mp4, .m4a, .m4v) / ISMV (.isma, .ismv) |Ja |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Ja |
| Matroska/WebM (.mkv) |Ja |
| WAVE/WAV (.wav) |Ja |
| QuickTime (.mov) |Ja |

> [!NOTE]
> Är en lista över filnamnstillägg som ofta påträffades. Media Encoder Standard stöder många andra (till exempel: .m2ts, .mpeg2video, .qt). Om du försöker att koda en fil och du får ett felmeddelande om formatet inte stöds, ger feedback [här](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Ljud-formaten i inkommande behållare
Media Encoder Standard stöder medför följande ljud format i inkommande behållare:

* MXF, GXF och QuickTime filer som har ljud spårar med överlagrad stereo eller 5.1-exempel

eller

* MXF, GXF och QuickTime filer där ljuduppspelningen utförs som separata PCM spår men kanalmappning (till stereo eller 5.1) härledas från filens metadata

Stöd för explicit/användardefinierat kanalmappning anges inom en snar framtid.

## <a name="input-video-codecs"></a>Inkommande video-codec
| Inkommande video-codec | Stöds |
| --- | --- | --- | --- |
| AVC 8-/ 10-bitars, upp till 4:2:2, inklusive AVCIntra |8-bitars 4:2:0 och 4:2:2 |
| Avid DNxHD (i MXF) |Ja |
| DVCPro/DVCProHD (i MXF) |Ja |
| Digital video (DV) (i AVI-filer) |Ja |
| JPEG 2000 |Ja |
| MPEG-2 (upp till 422 profil och högnivå, inklusive varianter, till exempel XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10) |Upp till 422 profil |
| MPEG-1 |Ja |
| WMV9-VC-1 |Ja |
| Canopus HQ/HQX |Nej |
| MPEG-4 del 2 |Ja |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |
| YUV420 okomprimerade eller mezzanine |Ja |
| Apple ProRes 422 |Ja |
| Apple ProRes 422 LT |Ja |
| Apple ProRes 422 HQ |Ja |
| Apple ProRes Proxy |Ja |
| Apple ProRes 4444 |Ja |
| Apple ProRes 4444 XQ |Ja |
| HEVC/H.265| Centrala profil|

## <a name="input-audio-codecs"></a>Inkommande ljud-codec
| Inkommande ljud-codec | Stöds |
| --- | --- | --- | --- |
| AAC (AAC LC, HE AAC och AAC-HEv2 upp till 5.1) |Ja |
| MPEG nivå 2 |Ja |
| MP3 (MPEG-1 ljud nivå 3) |Ja |
| Windows Media Audio |Ja |
| WAV PCM / |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |
| [Opus](http://go.microsoft.com/fwlink/?LinkId=822667) |Ja |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |
| AMR (anpassningsbar flera rate) |Ja |
| AES (SMPTE 331 M och 302 M, AES3 2003) |Nej |
| Dolby® E |Nej |
| Dolby® Digital (AC3) |Nej |
| Dolby® Digital Plus (E-AC3) |Nej |

## <a name="output-formats-and-codecs"></a>Output-format och codec
I följande tabell visas de codec och filformat som stöds för export.

| Filformat | Video-Codec | Ljud-Codec |
| --- | --- | --- |
| MP4 <br/><br/>(inklusive behållare i flera bithastigheter MP4) |H.264 (hög Main och baslinjen profiler) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2 TS |H.264 (hög Main och baslinjen profiler) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se också
[Koda innehåll på begäran med Azure Media Services](media-services-encode-asset.md)

[Koda med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

