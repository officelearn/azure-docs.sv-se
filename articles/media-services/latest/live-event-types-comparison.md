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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 93f01513841d1174fea796f1615ab05df0a41af4
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150383"
---
# <a name="live-event-types-comparison"></a>Live-händelse typer jämförelse

I Azure Media Services, en [direktsänd händelse](https://docs.microsoft.com/rest/api/media/liveevents) kan vara något av två typer: direktsänd kodning och direktlagringsdiskar. 

## <a name="types-comparison"></a>Jämförelse mellan typer 

I följande tabell jämförs funktionerna i Live händelsetyper. Typerna anges under skapas med hjälp av [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -en lokal livekodare skickar flera bithastighet. De infogade strömmarna passerar genom Live-händelsen utan vidare bearbetning. 
* **LiveEventEncodingType.Standard** – med en lokal livekodare skickar en enda bithastighet till direktsänd händelse och Media Services skapar strömmar med flera bithastigheter. Om flödet bidrag är av 720p eller högre upplösning på **Default720p** förinställning kommer koda av 6 lösning/bithastighet värdepar (information följer senare i artikeln).
* **LiveEventEncodingType.Premium1080p** – med en lokal livekodare skickar en enda bithastighet till direktsänd händelse och Media Services skapar strömmar med flera bithastigheter. Förinställningen Default1080p anger utdata lösning/bithastighet par (information följer senare i artikeln). 

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

Upplösningar och bithastigheter för utdata i utdata från livekodaren bestäms av den [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Om du använder en **Standard** live encoder (LiveEventEncodingType.Standard), kommer *Default720p* förinställning anger en uppsättning med 6 lösning/bithastighet par som beskrivs nedan. Annars, om du använder en **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), kommer *Default1080p* förinställning anger utdata lösning/bithastighet par.

> [!NOTE]
> Du kan inte använda Default1080p förinställning på en Live-händelse om den har ställts in för Standard live encoding – du får ett felmeddelande. Du får också ett fel om du försöker använda Default720p förinställning på en Premium1080p live-kodare.

### <a name="output-video-streams-for-default720p"></a>Video utdataströmmar för Default720p

Om flödet bidrag är av 720p eller högre upplösning på **Default720p** förinställning kommer koda flödet i de följande 6 skikt. I tabellen nedan är bithastighet i kbit/s, MaxFPS representerar den maximala tillåtna bildfrekvensen (i bildrutor per sekund), profil representerar H.264-profil som används.

| Bithastighet | Bredd | Höjd | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hög |
| 2200 |960 |540 |30 |Hög |
| 1350 |704 |396 |30 |Hög |
| 850 |512 |288 |30 |Hög |
| 550 |384 |216 |30 |Hög |
| 200 |340 |192 |30 |Hög |

> [!NOTE]
> Om du vill anpassa live förinställningen för kodningen kan öppna ett supportärende via Azure-portalen. Du bör ange den önskade tabellen med upplösning och bithastigheter. Kontrollera att det bara finns ett lager vid 720p och högst 6 lager. Även ange att du begär en förinställning för en Standard live-kodare.
> De specifika värdena av olika bithastigheter och upplösningar kan ändras över tid

### <a name="output-video-streams-for-default1080p"></a>Video utdataströmmar för Default1080p

Om flödet bidrag är av 1080p-upplösning som är den **Default1080p** förinställning kommer koda flödet i de följande 6 skikt.

| Bithastighet | Bredd | Höjd | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Hög |
| 3000 |1280 |720 |30 |Hög |
| 1600 |960 |540 |30 |Hög |
| 800 |640 |360 |30 |Hög |
| 400 |480 |270 |30 |Hög |
| 200 |320 |180 |30 |Hög |

> [!NOTE]
> Om du vill anpassa live förinställningen för kodningen kan öppna ett supportärende via Azure-portalen. Du bör ange den önskade tabellen med upplösning och bithastigheter. Kontrollera att det finns ett lager vid 1080p och högst 6 lager. Även ange att du begär en förinställning för en Premium1080p live-kodare.
> De specifika värdena av olika bithastigheter och upplösningar kan ändras över tid.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Utdata ljud Stream för Default720p och Default1080p

För både *Default720p* och *Default1080p* förinställningar, ljud kodas till stereo AAC-LC på 128 kbit/s. Samplingsfrekvensen följer som ljudspåret i bidraget feed.

## <a name="implicit-properties-of-the-live-encoder"></a>Implicit egenskaperna för live Encoding

I föregående avsnitt beskrivs egenskaperna för live-kodare som kan kontrolleras explicit, via förinställningen – till exempel hur många lager, upplösningar och bithastigheter för utdata. Det här avsnittet visar egenskaperna implicit.

### <a name="group-of-pictures-gop-duration"></a>Grupp av bilder (GOP) varaktighet

Live Encoding följer den [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) struktur bidrag feed - vilket innebär att utdata-lager har samma GOP varaktighet. Därför rekommenderas det att du konfigurerar den lokala kodare för att skapa ett bidrag feed som har åtgärdats GOP varaktighet (normalt 2 sekunder). Det säkerställer att de utgående HLS och MPEG-DASH-dataströmmar från tjänsten också har korrigerats GOP varaktigheter. Små variationer i GOP varaktigheter förmodligen tolereras av de flesta enheter.

### <a name="frame-rate"></a>Bildfrekvens

Live Encoding följer också varaktigheten för de enskilda bildrutorna i bidraget feed - vilket innebär att utdata-lager har bildrutor med samma varaktighet. Därför kan vi rekommenderar att du konfigurerar den lokala kodare för att skapa ett bidrag feed som har korrigerat bildfrekvens (högst 30 bildrutor per sekund). Det säkerställer att de utgående HLS och MPEG-DASH-dataströmmar från tjänsten också har åtgärdat ramens priserna varaktigheter. Små variationer i bildrutehastigheter kan tolereras av de flesta enheter, men det är inte säkert att livekodaren ger utdata som spelas upp korrekt. Din lokala livekodare bör inte nämligen (t.ex.) låg energinivå villkor) eller varierande bildfrekvens på något sätt.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Upplösning av bidraget feed och mata ut lager

Live Encoding är konfigurerad för att undvika upconverting bidraget feed. Den maximala upplösningen för utdata-lager kommer därför inte överstiga det bidrag feed.

Till exempel om du skickar ett bidrag feed på 720p till en Live-händelser som konfigurerats för Default1080p live encoding, utdata har endast 5 lager, från och med 720p på 3 Mbit/s, gå till 1080 p med 200 kbit/s. Eller om du skickar ett bidrag feed på 360p direktsänd kodning i en Live-händelser som konfigurerats för Standard utdata innehåller 3 lager (vid lösningar av 288p och 216p 192p). I degenererad fall, om du skickar en bidrag-feed med exempelvis 160 x 90 bildpunkter till en Standard livekodare innehåller utdata ett lager med 160 x 90 upplösning på samma bithastigheten som bidrag feed.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Bithastighet av bidraget feed och matar ut lager

Live Encoding är konfigurerad för att respektera bithastighet inställningarna i förinställningen oavsett bithastigheten bidrag feed. Därmed kan bithastigheten lager som utdata överskrida som bidrag feed. Till exempel om du skickar i ett bidrag feed med en upplösning på 720p med 1 Mbit/s utdata-lager är desamma som i den [tabell](live-event-types-comparison.md#output-video-streams-for-default720p) ovan.

## <a name="next-steps"></a>Nästa steg

[Direktsänd strömning översikt](live-streaming-overview.md)
