---
title: LiveEvent-typer för Azure Media Services | Microsoft-dokument
description: I Azure Media Services kan en livehändelse ställas in på antingen en *genomströmning* eller *livekodning*. Den här artikeln visar en detaljerad tabell som jämför typer av livehändelse.
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
ms.openlocfilehash: a28d4d96f643c12eeb6aa542db2c6af06f4fd954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244642"
---
# <a name="live-event-types-comparison"></a>Jämförelse av livehändelsetyper

I Azure Media Services kan en [Live Event](https://docs.microsoft.com/rest/api/media/liveevents) ställas in på antingen en *genomströmning* (en lokal live-kodare skickar en multibitrate-ström) eller *live-kodning* (en lokal live-kodare skickar en enda bitrate-ström). 

I den här artikeln jämförs funktionerna i typerna för liveevenemang.

## <a name="types-comparison"></a>Jämförelse mellan typer 

I följande tabell jämförs funktionerna i typerna för livehändelse. Typerna anges när [liveEventEncodingType skapas](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)under skapandet:

* **LiveEventEncodingType.None** - En lokal live-kodare skickar en multibitrate-ström. De intövda strömmarna passerar genom Live Event utan ytterligare bearbetning. Kallas även en direktsänd livehändelse.
* **LiveEventEncodingType.Standard** - En lokal live-kodare skickar en enda bitrate-ström till Live Event och Media Services skapar flera bithastighetsströmmar. Om bidragsflödet är 720p eller högre upplösning, kommer **standard720p-förinställningen** att koda en uppsättning par med 6 upplösning/bithastighet (detaljer följer senare i artikeln).
* **LiveEventEncodingType.Premium1080p** - En lokal live-kodare skickar en enda bitrate-ström till Live Event och Media Services skapar flera bithastighetsströmmar. Förinställningen Standard1080p anger utdatauppsättningen upplösning/bithastighetspar (detaljer följer senare i artikeln). 

| Funktion | Direkt livehändelse | Live-händelse för standard eller Premium1080p |
| --- | --- | --- |
| En bithastighetsindata kodas till flera bithastigheter i molnet |Inga |Ja |
| Maximal videoupplösning för bidragsflöde |4K (4096x2160 vid 60 bilder/sek) |1080p (1920x1088 vid 30 bilder/sek)|
| Rekommenderade maximala lager i bidragsflödet|Upp till 12|Ett ljud|
| Maximala lager i utdata| Samma som ingång|Upp till 6 (se Systemförinställningar nedan)|
| Maximal sammanlagd bandbredd för bidragsmatning|60 Mbit/s|Ej tillämpligt|
| Maximal bithastighet för ett enskilt lager i bidraget |20 Mbit/s|20 Mbit/s|
| Stöd för flera ljudspår på flera språk|Ja|Inga|
| Video codec som stöds |H.264/AVC och H.265/HEVC|H.264/AVC|
| Video codec-enheter som stöds|Samma som ingång|H.264/AVC|
| Videobitdjup, inmatning och utdata som stöds|Upp till 10-bitars inklusive HDR 10/HLG|8-bitars|
| Ljudkodatorer som stöds|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Ljudkodatorer som stöds|Samma som ingång|AAC-LC|
| Maximal videoupplösning för utdatavideo|Samma som ingång|Standard - 720p, Premium1080p - 1080p|
| Maximal bildhastighet för indatavideo|60 bilder/sekund|Standard eller Premium1080p - 30 bilder/sekund|
| Indataprotokoll|RTMP, fragmenterad-MP4 (Smooth Streaming)|RTMP, fragmenterad-MP4 (Smooth Streaming)|
| Pris|Se [prissidan](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken "Live Video"|Se [prissidan](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken "Live Video"|
| Maximal körtid| 24 timmar x 365 dagar, levande linjär | 24 timmar x 365 dagar, levande linjär (förhandsvisning)|
| Förmåga att passera genom inbäddade CEA 608/708 bildtexter data|Ja|Ja|
| Möjlighet att slå på Live Transkription|Ja|Ja|
| Stöd för att infoga skiffer|Inga|Inga|
| Stöd för annonssignalering via API| Inga|Inga|
| Stöd för annonssignalering via SCTE-35 in-band-meddelanden|Ja|Ja|
| Förmåga att återhämta sig från korta bås i bidrag foder|Ja|Delvis|
| Stöd för icke-enhetliga ingående gops|Ja|Nej – indata måste ha fast GOP-varaktighet|
| Stöd för indata för variabel bildhastighet|Ja|Nej – indata måste vara fast bildrutehastighet. Mindre variationer tolereras, till exempel under hög rörelse scener. Men bidragsflödet kan inte släppa bildrutefrekvensen (till exempel till 15 bildrutor/sekund).|
| Automatisk avstängning av Live Event när inmatningsflödet går förlorat|Inga|Efter 12 timmar, om det inte finns någon LiveOutput igång|

## <a name="system-presets"></a>Systemförinställningar

Upplösningarna och bithastigheterna i utdata från live-kodaren bestäms av [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Om du använder en **Standard** live-kodare (LiveEventEncodingType.Standard) anger förinställningen *Standard720p* en uppsättning par med 6 upplösning/bithastighet som beskrivs nedan. Annars, om du använder en **Premium1080p** live-kodare (LiveEventEncodingType.Premium1080p), anger förinställningen *Standard1080p* utdatauppsättningen upplösning/bitrate-par.

> [!NOTE]
> Du kan inte använda standardförinställningen1080p på en livehändelse om den har konfigurerats för standardkodning för live - du får ett felmeddelande. Du får också ett felmeddelande om du försöker tillämpa standard720p förinställningen på en Premium1080p live-kodare.

### <a name="output-video-streams-for-default720p"></a>Utdatavideoströmmar för standard720p

Om bidragsflödet är 720p eller högre upplösning kodar **standard720p-förinställningen** in matningen i följande 6 lager. I tabellen nedan är Bitrate i kbps, MaxFPS representerar den högsta tillåtna bildrutehastigheten (i bildrutor/sekund), Profil representerar den H.264-profil som används.

| Bitrate | Bredd | Höjd | MaxFPS (maxfps) | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hög |
| 2200 |960 |540 |30 |Hög |
| 1350 |704 |396 |30 |Hög |
| 850 |512 |288 |30 |Hög |
| 550 |384 |216 |30 |Hög |
| 200 |340 |192 |30 |Hög |

> [!NOTE]
> Om du behöver anpassa förinställningen för live-kodning öppnar du en supportbiljett via Azure Portal. Du bör ange den önskade tabellen med upplösning och bithastigheter. Kontrollera att det bara finns ett lager vid 720p och högst 6 lager. Ange också att du begär en förinställning för en standard-live-kodare.
> De specifika värdena för bithastigheter och upplösningar kan justeras med tiden

### <a name="output-video-streams-for-default1080p"></a>Utdatavideoströmmar för standard1080p

Om bidragsfeeden har en upplösning på 1080p **kodas** matningen i följande 6 lager.

| Bitrate | Bredd | Höjd | MaxFPS (maxfps) | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Hög |
| 3000 |1280 |720 |30 |Hög |
| 1600 |960 |540 |30 |Hög |
| 800 |640 |360 |30 |Hög |
| 400 |480 |270 |30 |Hög |
| 200 |320 |180 |30 |Hög |

> [!NOTE]
> Om du behöver anpassa förinställningen för live-kodning öppnar du en supportbiljett via Azure Portal. Du bör ange den önskade tabellen med upplösning och bithastigheter. Kontrollera att det bara finns ett lager vid 1080p och högst 6 lager. Ange också att du begär en förinställning för en Premium1080p live-kodare.
> De specifika värdena för bithastigheter och upplösningar kan justeras med tiden.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Utdataljudström för standard720p och default1080p

För både *Default720p* och *Default1080p* förinställningar kodas ljud till stereo AAC-LC vid 128 kbps. Provtagningshastigheten följer ljudspårets i bidragsflödet.

## <a name="implicit-properties-of-the-live-encoder"></a>Implicita egenskaper för live-kodaren

I föregående avsnitt beskrivs egenskaperna för den live-kodare som kan styras explicit, via förinställningen - till exempel antalet lager, upplösningar och bithastigheter. I det här avsnittet klargörs de implicita egenskaperna.

### <a name="group-of-pictures-gop-duration"></a>Grupp av bilder (GOP) varaktighet

Den levande kodaren följer [GOP-strukturen](https://en.wikipedia.org/wiki/Group_of_pictures) i bidragsflödet - vilket innebär att utdatalagren kommer att ha samma GOP-varaktighet. Därför rekommenderas att du konfigurerar den lokala kodaren för att producera ett bidragsflöde som har fast GOP-varaktighet (vanligtvis 2 sekunder). Detta säkerställer att de utgående HLS- och MPEG DASH-strömmarna från tjänsten också har fasta GOP-varaktigheter. Små variationer i GOP varaktigheter kommer sannolikt att tolereras av de flesta enheter.

### <a name="frame-rate"></a>Bildfrekvens

Den levande kodaren följer också varaktigheten av de enskilda videobildrutorna i bidragsflödet - vilket innebär att utdatalagren kommer att ha ramar med samma varaktighet. Därför rekommenderar vi att du konfigurerar den lokala kodaren för att producera ett bidragsflöde som har fast bildrutehastighet (högst 30 bildrutor/sekund). Detta säkerställer att de utgående HLS- och MPEG DASH-strömmarna från tjänsten också har fasta bildhastigheter. Små variationer i bildhastigheter kan tolereras av de flesta enheter, men det finns ingen garanti för att live-kodaren kommer att producera en utdata som spelas upp korrekt. Din lokala live-kodare bör inte släppa ramar (t.ex. under låga batteriförhållanden) eller varierar bildhastigheten på något sätt.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Upplösning av bidragsmatnings- och utmatningslager

Live-kodaren är konfigurerad för att undvika att efterföljande bidragsfeeden uppgraderas. Som ett resultat av detta kommer den maximala upplösningen för utdatalagren inte att överstiga bidragsmatningens.

Om du till exempel skickar en bidragsfeed vid 720p till en livehändelse som konfigurerats för livekodning för Standard1080p, har utdata bara 5 lager, med början med 720p vid 3 Mbps, som går ner till 1080p vid 200 kbit/s. Eller om du skickar ett bidragsflöde till 360p till en Live Event som konfigurerats för standarduppkodning, kommer utdata att innehålla 3 lager (med upplösningar på 288p, 216p och 192p). I det urartade fallet, om du skickar ett bidrag foder av, säg, 160x90 pixlar till en standard levande kodare, kommer utdata innehålla ett lager med 160x90 upplösning på samma bitrate som i bidraget foder.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Bithastighet av bidragsmatnings- och utmatningslager

Live-kodaren är konfigurerad för att uppfylla bithastighetsinställningarna i förinställningen, oavsett bithastigheten för bidragsfeeden. Som ett resultat av detta kan bithastigheten för utdatalagren överstiga bidragsmatningens. Om du till exempel skickar in ett bidragsflöde med en upplösning på 720p vid 1 Mbit/s förblir utdatalagren desamma som i [tabellen](live-event-types-comparison.md#output-video-streams-for-default720p) ovan.

## <a name="next-steps"></a>Nästa steg

[Översikt över livestreaming](live-streaming-overview.md)
