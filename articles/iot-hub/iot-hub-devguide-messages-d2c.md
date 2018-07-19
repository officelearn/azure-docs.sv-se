---
title: Förstå Azure IoT Hub-enhet till moln-meddelanden | Microsoft Docs
description: Developer guide – hur du använder enhet-till-moln-meddelanden med IoT Hub. Innehåller information om skicka både telemetri och icke-telemtry data och använda routning för att leverera meddelanden.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: be87b00f27f0d0b25cd77a0634ab1c653a85e5ac
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126450"
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Skicka meddelanden från enhet till moln till IoT Hub

Skicka meddelanden från enheten till molnet för att skicka time series-telemetri och aviseringar från dina enheter till lösningens backend-servrar, från en enhet till IoT hub. En beskrivning av andra alternativ för enhet-till-moln som stöds av IoT Hub finns i [enhet till molnet kommunikation vägledning][lnk-d2c-guidance].

Du skickar meddelanden från enheten till molnet via en enhet-riktade slutpunkt (**/devices/ {deviceId} / meddelanden/händelser**). Regler för vidarebefordran och vidarebefordra meddelandena till en tjänst-slutpunkter på IoT hub. Routningsregler Använd rubriker och brödtext för enhet-till-moln-meddelanden för att avgöra var du vill dirigera dem. Som standard dirigeras meddelanden till den inbyggda tjänst-riktade slutpunkten (**meddelanden/händelser**), som är kompatibel med [Händelsehubbar][lnk-event-hubs]. Du kan därför använda standard [Event Hubs-integrering och SDK: er] [ lnk-compatible-endpoint] att ta emot meddelanden från enheten till molnet i lösningens backend-servrar.

IoT Hub implementerar enhet-till-moln-meddelanden med hjälp av en strömmande meddelandemönster. Meddelanden från IoT Hub-enhet till molnet är mer som [Händelsehubbar] [ lnk-event-hubs] *händelser* än [Service Bus] [ lnk-servicebus] *meddelanden* eftersom det finns ett stort antal händelser som passerar genom den tjänst som kan läsas av flera läsare.

Meddelanden med IoT Hub enhet-till-molnet har följande egenskaper:

* Meddelanden från enheten till molnet är hållbar och sparade i en IoT-hubb standard **meddelanden/händelser** slutpunkt för upp till sju dagar.
* Meddelanden från enheten till molnet får innehålla högst 256 KB och kan grupperas i batchar att optimera skickar. Batchar får innehålla högst 256 KB.
* Enligt beskrivningen i den [styra åtkomsten till IoT Hub] [ lnk-devguide-security] avsnittet, IoT Hub kan per enhet autentisering och åtkomstkontroll.
* IoT Hub kan du skapa upp till 10 anpassade slutpunkter. Meddelanden levereras till slutpunkterna baserat på vägar som konfigurerats på din IoT-hubb. Mer information finns i [regler för routning](iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).
* IoT Hub kan miljontals simultant kopplade enheter (se [kvoter och begränsningar][lnk-quotas]).
* IoT Hub tillåter inte godtyckliga partitionering. Meddelanden från enheten till molnet partitioneras baserat på deras ursprung **deviceId**.

Mer information om skillnaderna mellan IoT Hub och Event Hubs finns i [jämförelse av Azure IoT Hub och Azure Event Hubs][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Skicka icke-telemetritrafik

Ofta skicka enheter förutom telemetri, meddelanden och förfrågningar som kräver separat körning och hantering i lösningens serverdel. Till exempel kritiska aviseringar som måste utlösa en specifik åtgärd i serverdelen. Du kan skriva en [routningsregel] [ lnk-devguide-custom] att skicka dessa typer av meddelanden till en slutpunkt för bearbetningen baserat på antingen ett sidhuvud på meddelandet eller ett värde i meddelandetexten.

Läs mer om det bästa sättet att bearbeta den här typen av meddelande i [självstudien: hur du bearbetar meddelanden från IoT Hub-enhet till molnet] [ lnk-d2c-tutorial] självstudien.

## <a name="route-device-to-cloud-messages"></a>Dirigera meddelanden från enheten till molnet

Har du två alternativ för att dirigera enhet-till-moln-meddelanden till dina backend-appar:

* Använda inbyggda [Event Hub-kompatibla slutpunkten] [ lnk-compatible-endpoint] aktivera backend-appar att läsa enhet-till-moln-meddelandena som tagits emot av hubben. Läs om den inbyggda Event Hub-kompatibla slutpunkten i [läsa meddelanden från enheten till molnet från den inbyggda slutpunkten][lnk-devguide-builtin].
* Använda regler för routning för att skicka meddelanden till anpassade slutpunkter i IoT hub. Anpassade slutpunkter konfigurera dina backend-appar kan läsa meddelanden från enhet till molnet med hjälp av Event Hubs, Service Bus-köer och Service Bus-ämnen. Läs om Routning och anpassade slutpunkter i [använda anpassade slutpunkter och routningsregler för meddelanden från enheten till molnet][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Egenskaper för skydd mot förfalskning

För att undvika enheten förfalskning i meddelanden från enheten till molnet, IoT Hub stämplar alla meddelanden med följande egenskaper:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

De första två innehåller den **deviceId** och **generationId** för den ursprungliga enheten enligt [identitet enhetsegenskaper][lnk-device-properties].

Den **ConnectionAuthMethod** egenskapen innehåller ett JSON-serialiserat objekt med följande egenskaper:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Nästa steg

Information om SDK: erna som du kan använda för att skicka meddelanden från enheten till molnet finns i [Azure IoT SDK: er][lnk-sdks].

Den [Snabbstarter] [ lnk-get-started] visar hur du skickar meddelanden från enheten till molnet från simulerade enheter. Mer information finns i den [Process IoT Hub enhet-till-moln-meddelanden med vägar] [ lnk-d2c-tutorial] självstudien.

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: quickstart-send-telemetry-node.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: tutorial-routing.md
