---
title: Felkoder för livehändelsen i Azure Media Services | Microsoft-dokument
description: I den här artikeln visas felkoder för livehändelse.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654587"
---
# <a name="media-services-live-event-error-codes"></a>Felkoder för Live Event för Media Services

Tabellerna i det här avsnittet innehåller felkoder för [Live Event.](live-events-outputs-concept.md)

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

När du prenumererar på [eventrutnätets](https://docs.microsoft.com/azure/event-grid/) händelser för en livehändelse kan du se något av följande fel från [liveEventConnectionRejected-händelsen.](media-services-event-schemas.md#liveeventconnectionrejected)

| Resultatkod | Beskrivning |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Felaktig intags-URL |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Kodar-IP finns inte i IP-listan för tillåt konfigurerad |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | RTMP-kodaren skickade inte kommandot setDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Codec-angiven stöds inte. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |Informationen om mediebeskrivningen togs inte emot innan de faktiska mediedata levererades.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |Antalet kvaliteter för ljud- eller videotyp överskred den högsta tillåtna gränsen.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |Den totala inkommande bithastigheten i en livehändelse eller kanaltjänst överskred den högsta tillåtna gränsen.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Tidsstämpeln för video- eller ljud-FLVTag är ogiltig från RTMP-kodaren. |
| MPE_INGEST_FRAMERATE_EXCEEDED | De inkommande kodaren intas strömmar med framerates överskred den högsta tillåtna 30fps för kodning live händelser / kanaler.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | De inkommande kodarna för introllade strömmar överskred följande tillåtna upplösningar: 1920x1088 för kodning av livehändelser/kanaler och 4096 x 2160 för direktupppassning av live-evenemang/kanaler.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderAvslutas

Du kan se något av följande fel från [liveEventEncoderDisconnected-händelsen.](media-services-event-schemas.md#liveeventencoderdisconnected)

|Resultatkod|Beskrivning|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|RTMP-sessionen har timeats ut efter att ha varit inaktiv för tillåtna tidsgränsen.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|Tidsstämpeln för video- eller ljud-FLVTag är ogiltig från RTMP-kodaren.|
|MPE_CAPACITY_LIMIT_REACHED|Kodaren skickar data för fort.|
|Okända felkoder|Dessa felkoder kan variera från minnesfel till duplicerade poster i hash-mappningen.|


## <a name="see-also"></a>Se även

[Felkoder för strömmande slutpunkt (Ursprung)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Nästa steg

[Självstudiekurs: Streama live med medietjänster](stream-live-tutorial-with-api.md)
