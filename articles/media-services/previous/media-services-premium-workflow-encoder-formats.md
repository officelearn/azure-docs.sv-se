---
title: Media Encoder Premium Workflow format och -codec | Microsoft Docs
description: Det här avsnittet ger en översikt över Media Encoder Premium Arbetsflödesformat format och -codec
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
ms.date: 01/15/2019
ms.author: juliako;anilmur
ms.openlocfilehash: 5059e4c00fc3bcbee76f8f5a3746b4d8783d901b
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359280"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Premium Workflow format och -codec

> [!NOTE]
> Premium-kodare frågor kan e- mepd@microsoft.com.
> 
> Media Encoder Premium Workflow mediebearbetare som beskrivs i det här avsnittet är inte tillgänglig i Kina. 
> 
> 

Det här dokumentet innehåller en lista över inkommande och utgående filformat och codec-enheter som stöds av den offentliga förhandsversionen av den **Media Encoder Premium Workflow** kodare.

[Media Encoder Premium Worflow indata format och -codec](#input_formats)

[Utdataformat för Media Encoder Premium Worflow och -codec](#output_formats)

**Media Encoder Premium Workflow** stöder textning som beskrivs i [detta](#closed_captioning) avsnittet. 

## <a id="input_formats"></a>Media Encoder Premium Workflow indata format och -codec

I följande avsnitt visas codec- och -filformat som har stöd för den här mediebearbetare som indata.

### <a name="input-containerfile-formats"></a>Ange Inmatningsbehållare/filformat

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* MPEG-2-transportströmmar
* MPEG-2-strömmar
* MPEG-4/MP4
* Windows Media/ASF
* AVI (okomprimerad 8-bitars/10-bitars)

### <a name="input-video-codecs"></a>Codec för Indatavideo

* AVC 8-bitars/10-bitars, upp till 4:2:2, inklusive AVCIntra
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* HEVC/H.265, Main och Main 10-profil
* JPEG2000
* MPEG-2 (upp till 422-profil och högnivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Indataljud

* AES (SMPTE 331 M och 302 M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1)
* MPEG-nivå 2
* MP3 (MPEG-1 ljud Layer 3)
* Windows Media Audio
* WAV/PCM

## <a id="output_format"></a>Utdata för Media Encoder Premium Arbetsflödesformat och codecs för

I följande avsnitt visas codec- och -filformat som stöds som utdata från den här medieprocessor.

### <a name="output-containerfile-formats"></a>Utdata Inmatningsbehållare/filformat

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM och AS02)
* DPP (inklusive AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (okomprimerad 8-bitars/10-bitars)
* Jämn direktuppspelning filformatet (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Codec för Indatavideo-utdata

* AVC (H.264; 8-bitars, upp till hög profil nivå 5.2; 4 K Ultra HD; AVC Intra)
* Avid DNxHD (in MXF)
* DVCPro/DVCProHD (in MXF)
* MPEG-2 (upp till 422-profil och högnivå, inklusive varianter som XDCAM, XDCAM HD, XDCAM IMX, CableLabs® och D10)
* MPEG-1
* Windows Media Video/VC-1
* JPEG-miniatyrgenerering
* – HEVC (H.265; 8-bitars och 10-bitars, huvud och Main 10-profil)

  Stöd för HDR 10 är tillgängligt i vissa fall, kontakta mepd@microsoft.com för mer information


### <a name="output-audio-codecs"></a>Utdata ljud-codec

* AES (SMPTE 331 M och 302 M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) upp till 7.1
* AAC (AAC-LC, AAC-HE och AAC-HEv2, upp till 5.1)
* MPEG-nivå 2
* MP3 (MPEG-1 ljud Layer 3)
* Windows Media Audio

>[!NOTE]
>Om du kodar Dolby® Digital (AC3), går det bara att skriva utdata i en ISO MP4-fil.

## <a id="closed_captioning"></a>Stöd för dold textning

På mata in **Media Encoder Premium Workflow** stöder:

1. SCC filer
2. SMPTE TT-filer
3. CEA-608/CEA-708 – som användardata (SFI meddelanden på H.264 elementär strömmar, ATSC/53, SCTE20) eller som extra data i MXF/GXF-filer
4. STL underrubrik filer

Följande alternativ finns på utdata:

1. CEA-608 CEA-708 översättning
2. CEA-608/CEA-708 passera (inbäddad i SFI meddelanden på H.264 elementär strömmar eller utförs som extra data i MXF-filer)
3. SCC
4. SMPTE TT (från källa CEA-608 per SMPTE RP2052, inklusive DFXP för filskapande)
5. SRT-undertextfil
6. DVB underrubrik strömmar

> [!NOTE]
> Inte alla utdataformaten ovan har stöd för leverans via strömning i Azure Media Services.

## <a name="known-issues"></a>Kända problem

Om din indatavideo inte innehåller innehåller textning, utdata tillgången fortfarande en tom TTML-fil. 

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

