---
title: Media Encoder Premium Workflow format och codecs | Microsoft Docs
description: Det här avsnittet ger en översikt över Media Encoder Premium Workflow format och codecenheter
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
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 87cd7c63939331190530a46071a6b4c40480562f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392942"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Premium Workflow format och codec

> [!NOTE]
> Media Encoder Premium Workflow medie processor som diskuteras i det här avsnittet är inte tillgänglig i Kina. 

Det här dokumentet innehåller en lista över in-och utdatafil format och codec-filer som stöds av den offentliga för hands versionen av **Media Encoder Premium Workflow** Encoder.

[Media Encoder Premium Workflow indataformat och codec](#input_formats)

Media Encoder Premium Workflow utdataformat och codec

**Media Encoder Premium Workflow** stöder dold textning som beskrivs i [det här](#closed_captioning) avsnittet. 

## <a id="input_formats"></a>Media Encoder Premium Workflow indataformat och codec

I följande avsnitt visas de codecenheter och fil format som medie processorn stöder som indata.

### <a name="input-containerfile-formats"></a>Indatafil/fil format

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* MPEG-2-överförings strömmar
* Program strömmar i MPEG-2
* MPEG-4/MP4
* Windows Media/ASF
* AVI (okomprimerad 8bit/10bit)

### <a name="input-video-codecs"></a>Codec för inspelning av video

* AVC 8-bitar/10-bit, upp till 4:2:2, inklusive AVCIntra
* Avid DNxHD (i MXF)
* DVCPro/DVCProHD (in MXF)
* HEVC/H. 265, Main och main 10 Profile
* JPEG2000
* MPEG-2 (upp till 422 profil och hög nivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Inmatade ljud-codec

* AES (SMPTE 331M och 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE och AAC-HEv2; upp till 5,1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media-ljud
* WAV/PCM

## <a id="output_format"></a>Media Encoder Premium Workflow utdataformat och codec

I följande avsnitt visas de codecenheter och fil format som stöds som utdata från medie processorn.

### <a name="output-containerfile-formats"></a>Utmatnings behållare/fil format

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM och AS02)
* DPP (inklusive AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (okomprimerad 8bit/10bit)
* Smooth Streaming fil format (PIFF 1,3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Codec för video utgång

* AVC (H. 264; 8-bitars, upp till hög profil, nivå 5,2; 4K Ultra HD; AVC Intra)
* Avid DNxHD (i MXF)
* DVCPro/DVCProHD (in MXF)
* MPEG-2 (upp till 422 profil och hög nivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10)
* MPEG-1
* Windows Media Video/VC-1
* Skapa JPEG-miniatyr
* HEVC (H. 265; 8 bitar och 10 bitar, Main och main 10 Profile)


### <a name="output-audio-codecs"></a>Ljud-codec för utdata

* AES (SMPTE 331M och 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) upp till 7,1
* AAC (AAC-LC, AAC-HE och AAC-HEv2; upp till 5,1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media-ljud

>[!NOTE]
>Om du kodar till Dolby® Digital (AC3) kan utdata bara skrivas till en ISO MP4-fil.

## <a id="closed_captioning"></a>Stöd för dold textning

Vid inmatningen stöder **Media Encoder Premium Workflow** :

1. SCC-filer
2. SMPTE-TT-filer
3. CEA-608/CEA-708 – transported as user data (SEI-meddelanden med H. 264-element strömmar, ATSC/53, SCTE20) eller transport som hjälp data i MXF/GXF-filer
4. STL-undertext-filer

Vid utdata är följande alternativ tillgängliga:

1. CEA – 608 till CEA-708 Översättning
2. CEA-608/CEA-708-vidarekoppling (inbäddat i SEI-meddelanden av data strömmar i H. 264-element eller som befordras som hjälp data i MXF-filer)
3. SCC
4. SMPTE-tidstext (från källa CEA-608 per SMPTE-RP2052; inklusive skapande av DFXP-fil)
5. SRT under rubrik fil
6. DVB-undertext strömmar

> [!NOTE]
> Inte alla dessa utdataformat stöds för leverans via direkt uppspelning i Azure Media Services.

## <a name="known-issues"></a>Kända problem

Om din indata-video inte innehåller dold textning innehåller utmatnings till gången fortfarande en tom TTML-fil. 

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna ett support ärende genom att gå till [nytt support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Utbildningsvägar för Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

