---
title: "Förstå Azure IoT Hub-enhet till moln messaging | Microsoft Docs"
description: "Utvecklare guide - hur du använder enhet till moln-meddelanden med IoT-hubben. Innehåller information om hur du skickar data både telemetri och icke-telemtry routning för att leverera meddelanden."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 48b904818c80b9175d45b88345634f11cf4a4812
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Skicka meddelanden från enhet till moln till IoT-hubb

Skicka meddelanden från enhet till moln för att skicka tidsserier telemetri och aviseringar från dina enheter till din lösningens serverdel, från en enhet till IoT-hubben. En beskrivning av andra enhet till moln-alternativ som stöds av IoT-hubb finns [enhet till moln kommunikation vägledning][lnk-d2c-guidance].

Du skickar meddelanden från enhet till moln via en enhet riktade slutpunkt (**/devices/ {deviceId} / meddelanden/händelser**). Routningsregler och vidarebefordra meddelanden till en av slutpunkterna service-riktade på din IoT-hubb. Regler för routning använda sidhuvuden och innehållet i meddelandena från enhet till moln för att avgöra var du vill dirigera dem. Som standard meddelanden dirigeras till den inbyggda tjänst-riktade slutpunkten (**meddelanden/händelser**), som är kompatibel med [Händelsehubbar][lnk-event-hubs]. Därför kan du använda standard [Händelsehubbar integrering och SDK] [ lnk-compatible-endpoint] att ta emot meddelanden från enhet till moln i din lösningens serverdel.

IoT-hubb implementerar enhet till moln-meddelanden med hjälp av en strömmande meddelandemönster används. IoT-hubb meddelanden från enhet till moln är mer som [Händelsehubbar] [ lnk-event-hubs] *händelser* än [Service Bus] [ lnk-servicebus] *meddelanden* att en stor volym med händelser som passerar den tjänst som kan läsas av flera läsare.

Enhet till moln meddelanden med IoT-hubben har följande egenskaper:

* Meddelanden från enhet till moln är beständiga och behållas i en IoT-hubb standard **meddelanden/händelser** slutpunkt för upp till sju dagar.
* Meddelanden från enhet till moln kan innehålla högst 256 KB och kan grupperas i batchar att optimera skickar. Batchar kan innehålla högst 256 KB.
* Enligt beskrivningen i den [styra åtkomsten till IoT-hubb] [ lnk-devguide-security] avsnittet, IoT-hubb kan per enhet autentisering och åtkomstkontroll.
* IoT-hubb kan du skapa upp till 10 anpassade slutpunkter. Meddelanden levereras till slutpunkterna baserat på vägar som konfigurerats på din IoT-hubb. Mer information finns i [regler för routning](#routing-rules).
* IoT-hubb kan miljontals samtidigt anslutna enheter (se [kvoter och begränsning][lnk-quotas]).
* IoT-hubb kan inte godtycklig partitionering. Meddelanden från enhet till moln partitioneras baserat på deras ursprung **deviceId**.

Mer information om skillnaderna mellan IoT-hubb och Händelsehubbar finns [jämförelse av Azure IoT Hub och Azure Event Hubs][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Skicka ej telemetri trafik

Ofta skicka enheter förutom telemetri, meddelanden och förfrågningar som kräver separat utförande och hantering i lösningens serverdel. Till exempel kritiska aviseringar som skall utlösa en specifik åtgärd i serverdelen. Du kan skriva en [routningsregel] [ lnk-devguide-custom] att skicka dessa typer av meddelanden till en slutpunkt som är dedikerad till bearbetningen baserat på antingen ett sidhuvud på meddelandet eller ett värde i meddelandetexten.

Mer information om det bästa sättet att bearbeta den här typen av meddelande finns i [Självstudier: bearbeta meddelanden från enhet till moln IoT-hubb] [ lnk-d2c-tutorial] kursen.

## <a name="route-device-to-cloud-messages"></a>Vidarebefordra meddelanden från enhet till moln

Har du två alternativ för att dirigera enhet till moln-meddelanden till backend-appar:

* Använd inbyggt [Event Hub-kompatibel endpoint] [ lnk-compatible-endpoint] aktivera backend-appar att läsa de enhet till moln har tagits emot av hubben. Mer information om inbyggda Event Hub-kompatibel slutpunkten, se [läsa meddelanden från enhet till moln från inbyggd slutpunkt][lnk-devguide-builtin].
* Använda regler för routning för att skicka meddelanden till anpassade slutpunkter i din IoT-hubb. Anpassade slutpunkter aktivera dina backend-appar att läsa meddelanden från enhet till moln med Händelsehubbar, Service Bus-köer eller Service Bus-ämnen. Mer information om Routning och anpassade slutpunkter, se [använda anpassade slutpunkter och routningsregler för meddelanden från enhet till moln][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Skydd mot förfalskning egenskaper

För att undvika enheten förfalskning i meddelanden från enhet till moln, IoT-hubb stämplar alla meddelanden med följande egenskaper:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

De första två innehåller den **deviceId** och **generationId** på den ursprungliga enheten enligt [identitet enhetsegenskaper][lnk-device-properties].

Den **ConnectionAuthMethod** -egenskapen innehåller ett serialiserat JSON-objekt med följande egenskaper:

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Nästa steg

Information om SDK: erna som du kan använda för att skicka meddelanden från enhet till moln finns [Azure IoT SDK][lnk-sdks].

Den [Kom igång] [ lnk-get-started] självstudiekurser visar hur du skickar meddelanden från enhet till moln från både simulerade och fysiska enheter. Mer information finns i [processen IoT Hub-enhet till moln meddelanden med hjälp av vägar] [ lnk-d2c-tutorial] kursen.

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
