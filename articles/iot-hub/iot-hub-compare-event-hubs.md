---
title: "Jämföra Azure IoT-hubb till Azure Event Hubs | Microsoft Docs"
description: "En jämförelse av tjänsterna IoT-hubb och Event Hubs Azure syntaxmarkering funktionella skillnader och användningsfall. Jämförelse innehåller protokoll som stöds, hantering, övervakning, och filöverföringar."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: b515e05d16dda83c7d865113d5d3578c44be084f
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Jämförelse mellan Azure IoT-hubb och Azure Event Hubs
En av de huvudsakliga användningsområden för IoT-hubben är att samla in telemetri från enheter. Därför IoT-hubben är ofta jämfört med [Azure Event Hubs][Azure Event Hubs]. Händelsehubbar är en tjänst som gör det händelse- och telemetriingång till molnet i massiv skala med kort svarstid och hög tillförlitlighet för händelsebearbetning som IoT-hubb.

Tjänsterna har emellertid många skillnader som beskrivs i följande tabell:

| Område | IoT Hub | Händelsehubbar |
| --- | --- | --- |
| Kommunikationsmönster | Aktiverar [enhet till moln kommunikation] [ lnk-d2c-guidance] (messaging filen överföringar och rapporterade egenskaper) och [moln till enhet kommunikation] [ lnk-c2d-guidance] (direct metoder, egenskaper, messaging). |Aktiverar endast händelse ingång (vanligtvis betraktas för scenarier med enhet till moln). |
| Information om enhetens tillstånd | [Enheten twins] [ lnk-twins] kan lagra och hämta information om enhetens tillstånd. | Ingen statusinformation för enheten kan inte lagras. |
| Enheten protokollstöd |Stöder MQTT MQTT över WebSockets, AMQP, AMQP via WebSockets och HTTPS. Dessutom IoT-hubb fungerar med den [Azure IoT-protokollgatewayen][lnk-azure-protocol-gateway], en anpassningsbara protokollimplementering gateway att stödja anpassade protokoll. |Stöder AMQP AMQP via WebSockets och HTTPS. |
| Säkerhet |Ger identitet per enhet och återkallningsbar åtkomstkontroll. Finns det [Säkerhetsavsnittet i utvecklarhandboken för IoT-hubb]. |Ger Event Hubs hela [delade åtkomstprinciper][Event Hubs - security], med begränsad återkallade stöder via [utgivarens principer][Event Hubs publisher policies]. IoT-lösningar krävs ofta att implementera en anpassad lösning för att stödja per enhet autentiseringsuppgifter och åtgärder som skydd mot förfalskning. |
| Övervakning av åtgärder |Gör det möjligt för IoT-lösningar att prenumerera på en omfattande uppsättning enheten identity management och anslutningar händelser som enskild enhet autentiseringsfel, begränsning och felaktigt format undantag. Dessa händelser kan du snabbt identifiera problem med nätverksanslutningen på enskilda enhetsnivå. |Visar endast sammanställd mått. |
| Skala |Är optimerat stöd miljoner samtidigt anslutna enheter. |Mätare anslutningar enligt [Händelsehubbar i Azure-kvoter][Azure Event Hubs quotas]. Å andra sidan kan Händelsehubbar du ange partition för varje meddelande som skickas. |
| Enheten SDK |Ger [enheten SDK] [ Azure IoT SDKs] för en stor mängd olika plattformar och språk, förutom direkt MQTT AMQP och HTTPS-API: er. |Stöds på .NET, Java och C, dessutom AMQP och HTTPS send-gränssnitt. |
| Ladda upp filen |Gör det möjligt för IoT-lösningar att överföra filer från enheter till molnet. Innehåller en fil aviseringsslutpunkten för arbetsflödet integrering och ett åtgärder övervakning kategori för felsökning stöd. | Stöds inte. |
| Skicka meddelanden till flera slutpunkter | Upp till 10 anpassade slutpunkter stöds. Reglerna bestämmer hur meddelanden skickas till anpassade slutpunkter. Mer information finns i [skicka och ta emot meddelanden med IoT-hubben][lnk-devguide-messaging]. | Kräver ytterligare kod skrivs och är värd för meddelandesändning. |

Även om endast användningsfall är enhet till moln telemetriingång Sammanfattningsvis ger en tjänst som är avsedd för IoT-enhetsanslutning IoT-hubb. Fortsätter den att expandera värderingsförslag för dessa scenarier med IoT-specifika funktioner. Händelsehubbar är utformad för händelsen ingång i massiv skala, både i samband med scenarier bland datacenter och intra-datacenter.

Det är inte ovanligt att använda både IoT-hubb och Händelsehubbar i samma lösning. IoT-hubb hanterar enhet till moln-kommunikation och Händelsehubbar hanterar senare skede händelse ingång till realtidsbearbetning motorer.

### <a name="next-steps"></a>Nästa steg
Mer information om hur du planerar distributionen av IoT-hubb finns [skalning, hög tillgänglighet och Katastrofåterställning][lnk-scaling].

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till enheter med Azure IoT kant][lnk-iotedge]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Säkerhetsavsnittet i utvecklarhandboken för IoT-hubb]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-features.md#event-publishers
[Azure Event Hubs quotas]: ../event-hubs/event-hubs-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
