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
ms.openlocfilehash: 9952a7bbac1eb79de0d3425f839e3bd30196844e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243898"
---
# <a name="live-event-types-comparison"></a>Live-händelse typer jämförelse

I Azure Media Services, en [direktsänd händelse](https://docs.microsoft.com/rest/api/media/liveevents) kan vara något av två typer: direktsänd kodning och direktlagringsdiskar. 

## <a name="types-comparison"></a>Jämförelse mellan typer 

I följande tabell jämförs funktionerna i de två typerna av direktsänd händelse.

| Funktion | Direktsändningen direkt | Standard Live-händelse |
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
| Maximal video upplösning på utdata-video|Samma som indata|720p (på 30 bilder per sekund)|
| Inkommande protokoll|RTMP, fragmenterad-MP4 (Smooth Streaming)|RTMP, fragmenterad-MP4 (Smooth Streaming)|
| Pris|Se den [prissättningssidan](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken ”Live-Video”|Se den [prissättningssidan](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken ”Live-Video”|
| Maximal körtid| 24 timmar x 365 dagar, live linjär | Upp till 24 timmar|
| Möjligheten att överföra via embedded CEA 608/708 bildtexter data|Ja|Ja|
| Stöd för att infoga pekdatorer|Nej|Nej|
| Stöd för ad-signalering via API: et| Nej|Nej|
| Stöd för ad-signalering via SCTE 35 in-band-meddelanden|Ja|Ja|
| Återställa från kort bås i bidrag feed|Ja|Nej (direktsänd händelse börjar slating efter 6 + sekunder utan indata)|
| Stöd för icke-enhetlig inkommande GOPs|Ja|Nej – indata måste ha fast GOP varaktighet|
| Stöd för variabeln ramens rate indata|Ja|Nej – indata som måste åtgärdas bildfrekvens. Smärre variationer användas, till exempel under hög rörelse scener. Men bidrag feed kan därför inte släppas bildfrekvens (till exempel till 15 bildrutor per sekund).|
| Automatisk-avslutning av Live-händelse när indata-flöde går förlorad|Nej|Efter 12 timmar, om det finns inga LiveOutput som körs|

## <a name="system-presets"></a>Systeminställningar

När du använder live encoding (direktsänd händelse inställd **Standard**), förinställningen för kodningen definierar hur inkommande strömmen kodas i flera olika bithastigheter eller lager. För närvarande den enda tillåtna värdet för förinställningen är *Default720p* (standard).

**Default720p** kommer koda videon till följande 6 lagren.

### <a name="output-video-stream"></a>Utdata Video Stream

| Bithastighet | Bredd | Höjd | MaxFPS | Profil | Utdatanamnet för Stream |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hög |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Hög |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Hög |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Hög |Video_512x288_850kbps |
| 550 |384 |216 |30 |Hög |Video_384x216_550kbps |
| 200 |340 |192 |30 |Hög |Video_340x192_200kbps |

> [!NOTE]
> Om du vill använda en anpassad förinställning för live encoding kan du kontakta amshelp@microsoft.com. Du bör ange den önskade tabellen i upplösning och bithastighet. Kontrollera att det finns ett lager vid 720p och högst 6 lager.

### <a name="output-audio-stream"></a>Utdata ljud Stream

Ljud kodas till stereo AAC-LC på 128 kbit/s, samplingsfrekvensen 48 kHz.

## <a name="next-steps"></a>Nästa steg

[Direktsänd strömning översikt](live-streaming-overview.md)
