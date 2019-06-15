---
title: Azure Media Services LiveEvent typer | Microsoft Docs
description: Den här artikeln innehåller en detaljerad tabell som jämför LiveEvent typer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: bd4899374c06246ddd4d5fa81d0f6e3a6a1e7017
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075011"
---
# <a name="live-event-types-comparison"></a>Live-händelse typer jämförelse

I Azure Media Services, en [direktsänd händelse](https://docs.microsoft.com/rest/api/media/liveevents) kan vara något av två typer: direktsänd kodning och direktlagringsdiskar. 

## <a name="types-comparison"></a>Jämförelse mellan typer 

I följande tabell jämförs funktionerna i Live händelsetyper.

| Funktion | Direktsändningen direkt | Standard eller Premium1080p Live-händelse |
| --- | --- | --- |
| Enkel bithastighet indata kodas till flera olika bithastigheter i molnet |Nej |Ja |
| Maximal upplösning som video för bidrag feed |4K (4096 x 2160 på 60 bildrutor per sekund) |1080p (1920 x 1088 på 30 bilder per sekund)|
| Rekommenderade maximala lager i bidrag feed|Upp till 12|En med endast ljud|
| Maximal lager i utdata| Samma som indata|Upp till 6 (se nedan systeminställningar)|
| Maximala sammanlagda bandbredden av bidraget feed|60 Mbit/s|Gäller inte|
| Högsta bithastighet för ett enskilt lager i bidrag |20 Mbit/s|20 Mbit/s|
| Stöd för flera språk ljudspår|Ja|Nej|
| Stöd för inkommande codec för indatavideo |H.264/AVC och H.265/HEVC|H.264/AVC|
| Codec för indatavideo stöds utdata|Samma som indata|H.264/AVC|
| Stöd för video bitar, indata och utdata|Upp till 10-bitars inklusive HDR 10/HLG|8-bitars|
| Stöd för inkommande ljud-codec|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Ljud stöds utdata-codecs|Samma som indata|AAC-LC|
| Maximal video upplösning på utdata-video|Samma som indata|Standard – 720p Premium1080p - 1080p|
| Maximal bildfrekvens av indatavideon|60 bildrutor per sekund|Standard eller Premium1080p - 30 bilder per sekund|
| Inkommande protokoll|RTMP, fragmenterad-MP4 (Smooth Streaming)|RTMP, fragmenterad-MP4 (Smooth Streaming)|
| Pris|Se den [prissättningssidan](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken ”Live-Video”|Se den [prissättningssidan](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken ”Live-Video”|
| Maximal körtid| 24 timmar x 365 dagar, live linjär | Upp till 24 timmar|
| Möjligheten att överföra via embedded CEA 608/708 bildtexter data|Ja|Ja|
| Stöd för att infoga pekdatorer|Nej|Nej|
| Stöd för ad-signalering via API: et| Nej|Nej|
| Stöd för ad-signalering via SCTE 35 in-band-meddelanden|Ja|Ja|
| Återställa från kort bås i bidrag feed|Ja|Delvis|
| Stöd för icke-enhetlig inkommande GOPs|Ja|Nej – indata måste ha fast GOP varaktighet|
| Stöd för variabeln ramens rate indata|Ja|Nej – indata som måste åtgärdas bildfrekvens. Smärre variationer användas, till exempel under hög rörelse scener. Men bidrag feeden det går inte att släppa bildfrekvensen (till exempel på 15 bildrutor per sekund).|
| Automatisk-avslutning av Live-händelse när indata-flöde går förlorad|Nej|Efter 12 timmar, om det finns inga LiveOutput som körs|

## <a name="system-presets"></a>Systeminställningar

Upplösningar och bithastigheter för utdata i utdata från livekodaren bestäms av den [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Om du använder en **Standard** live encoder (LiveEventEncodingType.Standard), kommer *Default720p* förinställning anger en uppsättning med 6 lösning/bitars rate-par som beskrivs nedan. Annars, om du använder en **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), kommer *Default1080p* förinställda specifiesthe utdata av lösning/bitars rate värdepar. 

### <a name="output-video-streams-for-default720p"></a>Video utdataströmmar för Default720p

**Default720p** kommer koda indatavideon till följande 6 lagren.

| Bithastighet | Bredd | Höjd | MaxFPS | Profil | Utdatanamnet för Stream |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hög |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Hög |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Hög |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Hög |Video_512x288_850kbps |
| 550 |384 |216 |30 |Hög |Video_384x216_550kbps |
| 200 |340 |192 |30 |Hög |Video_340x192_200kbps |

> [!NOTE]
> Om du vill anpassa live förinställningen för kodningen kan öppna ett supportärende via Azure-portalen. Du bör ange den önskade tabellen med upplösning och bithastigheter. Kontrollera att det bara finns ett lager vid 720p och högst 6 lager. Även ange att du begär en förinställning för en Standard live-kodare.

### <a name="output-video-streams-for-default1080p"></a>Video utdataströmmar för Default1080p

**Default1080p** kommer koda indatavideon till följande 6 lagren.

| Bithastighet | Bredd | Höjd | MaxFPS | Profil | Utdatanamnet för Stream |
| --- | --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Hög |Video_1920x1080_5500kbps |
| 3000 |1280 |720 |30 |Hög |Video_1280x720_3000kbps |
| 1600 |960 |540 |30 |Hög |Video_960x540_1600kbps |
| 800 |640 |360 |30 |Hög |Video_640x360_800kbps |
| 400 |480 |270 |30 |Hög |Video_480x270_400kbps |
| 200 |320 |180 |30 |Hög |Video_320x180_200kbps |

> [!NOTE]
> Om du vill anpassa live förinställningen för kodningen kan öppna ett supportärende via Azure-portalen. Du bör ange den önskade tabellen med upplösning och bithastigheter. Kontrollera att det finns ett lager vid 1080p och högst 6 lager. Även ange att du begär en förinställning för en Premium1080p live-kodare.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Utdata ljud Stream för Default720p och Default1080p

För både *Default720p* och *Default1080p* förinställningar, ljud kodas till stereo AAC-LC på 128 kbit/s, samplingsfrekvens 48 kHz.

## <a name="next-steps"></a>Nästa steg

[Direktsänd strömning översikt](live-streaming-overview.md)
