---
title: Fel koder för Azure Media Services Live-händelse | Microsoft Docs
description: Den här artikeln innehåller fel koder för Live-händelser.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654587"
---
# <a name="media-services-live-event-error-codes"></a>Fel koder för Media Services Live-händelse

Tabellerna i det här avsnittet visar fel koderna för [direkt händelser](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

När du prenumererar på [Event Grid](https://docs.microsoft.com/azure/event-grid/) händelser för en Live-händelse kan du se något av följande fel från händelsen [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected) .

| Resultat kod | Beskrivning |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Felaktig hämtnings-URL |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Kodarens IP-adress finns inte i listan över tillåtna IP-adresser |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | RTMP-kodaren skickade inte setDataFrame-kommandot. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Angiven Codec stöds inte. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |Medie beskrivnings informationen togs inte emot innan de faktiska medie data levererades.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |Antalet kvaliteter för ljud-eller video typ överskrider den maximalt tillåtna gränsen.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |Den totala inkommande bit hastigheten i en Live-händelse eller kanal tjänst överskred den maximalt tillåtna gränsen.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Tidsstämpeln för video-eller ljud-FLVTag är ogiltig från RTMP-kodaren. |
| MPE_INGEST_FRAMERATE_EXCEEDED | Inkommande kodare inmatade strömmar med ram-indata överskrider den högsta tillåtna 30fps för kodning av Live-händelser/-kanaler.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | Inkommande kodade inmatade data strömmar översteg följande tillåtna lösningar: 1920x1088 för att koda Live-händelser/kanaler och 4096 x 2160 för direkt sändnings händelser/kanaler.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Du kan se något av följande fel från händelsen [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) .

|Resultat kod|Beskrivning|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|RTMP-sessionen nådde tids gränsen efter inaktivitet för tillåten tids gräns.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|Tidsstämpeln för video-eller ljud-FLVTag är ogiltig från RTMP-kodaren.|
|MPE_CAPACITY_LIMIT_REACHED|Kodaren skickar data för snabbt.|
|Okända felkoder|Dessa felkoder kan vara från minnes fel till dubbla poster i hash-mappningen.|


## <a name="see-also"></a>Se även

[Felkoder för strömnings slut punkt (ursprung)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Nästa steg

[Självstudie: strömma live med Media Services](stream-live-tutorial-with-api.md)
