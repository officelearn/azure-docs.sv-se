---
title: Standard kodare format och codec-Azure
description: Den här artikeln innehåller en lista över de vanligaste import-och export fil formaten som du kan använda med StandardEncoderPreset.
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
ms.date: 02/10/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: dd8c38f9f08bbcaccc39d2f0d1843af720c12932
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053243"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standardformat för kodare och codec

Den här artikeln innehåller en lista över de vanligaste import-och export fil formaten som du kan använda med [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Information om hur du skapar anpassade för inställningar med hjälp av **StandardEncoderPreset**finns i [skapa en transformering med en anpassad för inställning](customize-encoder-presets-how-to.md).

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

### <a name="audio-formats-in-input-containers"></a>Ljud format i indata-behållare

Standard-kodare stöder följande ljud format i indatafiler:

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
| HEVC/H. 265| Huvud profil|

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

## <a name="next-steps"></a>Nästa steg

[Skapa en transformering med en anpassad för inställning](customize-encoder-presets-how-to.md)
