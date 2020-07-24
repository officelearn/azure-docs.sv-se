---
title: Fel koder för Azure Media Services Live-händelse | Microsoft Docs
description: Den här artikeln innehåller fel koder för Live-händelser.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/4/2020
ms.author: inhenkel
ms.openlocfilehash: 8f86c157652f6219afdf1df1da9b8ad385f059aa
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87000959"
---
# <a name="media-services-live-event-error-codes"></a>Fel koder för Media Services Live-händelse

Tabellerna i det här avsnittet visar fel koderna för [direkt händelser](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

När du prenumererar på [Event Grid](../../event-grid/index.yml) händelser för en Live-händelse kan du se något av följande fel från händelsen [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected) .

| Resultatkod | Beskrivning |
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
| MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV | Live-händelsen har fått en stor mängd ljuddata samtidigt, eller en stor mängd video data utan några viktiga bild rutor. Vi har frånkopplat kodaren för att ge den möjlighet att försöka igen med rätt data. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Du kan se något av följande fel från händelsen [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) .

|Resultatkod|Beskrivning|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|RTMP-sessionen nådde tids gränsen efter inaktivitet för tillåten tids gräns.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|Tidsstämpeln för video-eller ljud-FLVTag är ogiltig från RTMP-kodaren.|
|MPE_CAPACITY_LIMIT_REACHED|Kodaren skickar data för snabbt.|
|Okända felkoder|Dessa felkoder kan vara från minnes fel till dubbla poster i hash-mappningen.|


## <a name="see-also"></a>Se även

[Felkoder för strömnings slut punkt (ursprung)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Nästa steg

[Självstudie: strömma live med Media Services](stream-live-tutorial-with-api.md)
