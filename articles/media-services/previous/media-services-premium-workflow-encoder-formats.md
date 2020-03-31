---
title: Media Encoder Premium arbetsflöde format och codecs | Microsoft-dokument
description: Det här avsnittet innehåller en översikt över format och codec-format för Media Encoder Premium-arbetsflödesformat
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269762"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Format och codec-kodc för Premium-arbetsflöde för Media Encoder

> [!NOTE]
> Media Encoder Premium Workflow media processor diskuteras i det här avsnittet är inte tillgänglig i Kina. 

Det här dokumentet innehåller en lista över indata- och utdatafilformat och codec-enheter som stöds av den offentliga förhandsversionen av **Media Encoder Premium Workflow-kodaren.**

[Mediekodare Premium arbetsflöde inmatningsformat och codec-enheter](#input_formats)

Media Encoder Premium Arbetsflöde utdataformat och codec-enheter

**Media Encoder Premium Workflow** stöder dold textning som beskrivs i [det här](#closed_captioning) avsnittet. 

## <a name="media-encoder-premium-workflow-input-formats-and-codecs"></a><a id="input_formats"></a>Mediekodare Premium arbetsflöde inmatningsformat och codec-enheter

I följande avsnitt visas de codec-enheter och filformat som medieprocessorn stöder som indata.

### <a name="input-containerfile-formats"></a>Inmatningscontainer/filformat

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF (på andra sätt)
* MPEG-2 Transportströmmar
* MPEG-2 Program Strömmar
* MPEG-4/MP4
* Windows Media/ASF
* AVI (Okomprimerad 8bit/10bit)

### <a name="input-video-codecs"></a>Codec för indatavideo

* AVC 8-/10-bitars, upp till 4:2:2, inklusive AVCIntra
* Avid DNxHD (i MXF)
* DVCPro/DVCProHD (i MXF)
* HEVC/H.265, Main och Main 10 Profil
* JPEG2000
* MPEG-2 (upp till 422 profil och hög nivå; inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Codec för indataljud

* AES (SMPTE 331M och 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio
* WAV/PCM

## <a name="media-encoder-premium-workflow-output-formats-and-codecs"></a><a id="output_format"></a>Media Encoder Premium Arbetsflöde utdataformat och codec-enheter

I följande avsnitt visas de codec-enheter och filformat som stöds som utdata från den här medieprocessorn.

### <a name="output-containerfile-formats"></a>Utdatabehållare/filformat

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM och AS02)
* DPP (inklusive AS11)
* GXF (på andra sätt)
* MPEG-4/MP4
* Windows Media/ASF
* AVI (Okomprimerad 8bit/10bit)
* Jämnt direktuppspelningsfilformat (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Utdata video codec

* AVC (H.264; 8-bitars; upp till hög profil, nivå 5.2; 4K Ultra HD; AVC Intra)
* Avid DNxHD (i MXF)
* DVCPro/DVCProHD (i MXF)
* MPEG-2 (upp till 422 profil och hög nivå; inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10)
* MPEG-1
* Windows Media Video/VC-1
* Skapa JPEG-miniatyrer
* HEVC (H.265; 8 bitars och 10 bitars, Main och Main 10 Profil)


### <a name="output-audio-codecs"></a>Utdata ljud codec

* AES (SMPTE 331M och 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) upp till 7,1
* AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio

>[!NOTE]
>Om du kodar till Dolby® Digital (AC3) kan utdata bara skrivas in i en ISO MP4-fil.

## <a name="support-for-closed-captioning"></a><a id="closed_captioning"></a>Stöd för dold textning

Vid intag stöder **Media Encoder Premium Workflow:**

1. SCC-filer
2. SMPTE-TT-filer
3. CEA-608/CEA-708 – transporteras som användardata (SEI-meddelanden från H.264 elementära strömmar, ATSC/53, SCTE20) eller transporteras som underordnade data i MXF/GXF-filer
4. STL undertextfiler

Vid utdata är följande alternativ tillgängliga:

1. CEA-608 till CEA-708 översättning
2. CEA-608/CEA-708 passerar (inbäddad i SEI-meddelanden av H.264 elementära strömmar, eller transporteras som underordnade data i MXF filer)
3. Scc
4. SMPTE-tidsinställda text (från källan CEA-608 per SMPTE RP2052; inklusive skapande av DFXP-filer)
5. SRT Undertextfil
6. DVB undertext strömmar

> [!NOTE]
> Alla utdataformat ovan stöds inte för leverans via direktuppspelning i Azure Media Services.

## <a name="known-issues"></a>Kända problem

Om indatavideon inte innehåller dold textning innehåller utdatatillgången fortfarande en tom TTML-fil. 

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna en supportbiljett genom att navigera till [Ny supportförfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

