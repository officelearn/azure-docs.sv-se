---
title: Förstå Azures IoT Hub meddelande format | Microsoft Docs
description: Guide för utvecklare – beskriver formatet och förväntat innehåll i IoT Hub meddelanden.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 28537ac2389fbb1ca43ca4014515564bddeba4ce
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872485"
---
# <a name="create-and-read-iot-hub-messages"></a>Skapa och läsa IoT Hub-meddelanden

För att stödja sömlös interoperabilitet mellan protokoll, definierar IoT Hub ett gemensamt meddelande format för alla protokoll som riktar sig mot enheter. Detta meddelande format används för både [enhet-till-moln-routning](iot-hub-devguide-messages-d2c.md) och meddelanden från [moln till enhet](iot-hub-devguide-messages-c2d.md) . 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementerar enhets-till-moln-meddelanden med hjälp av ett mönster för strömnings meddelande hantering. IoT Hub s meddelanden från enhet till molnet är mer som [Event Hubs](/azure/event-hubs/) *händelser* än [Service Bus](/azure/service-bus-messaging/) *meddelanden* på att det finns en stor mängd händelser som passerar genom tjänsten som kan läsas av flera läsare.

Ett IoT Hub meddelande består av:

* En fördefinierad uppsättning *system egenskaper* enligt listan nedan.

* En uppsättning *program egenskaper*. En ord lista med sträng egenskaper som programmet kan definiera och komma åt, utan att du behöver deserialisera meddelande texten. IoT Hub ändrar aldrig dessa egenskaper.

* En täckande binär brödtext.

Egenskaps namn och värden får bara innehålla ASCII-tecken, ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` plus när du skickar meddelanden från enheten till molnet med hjälp av HTTPS-protokollet eller skickar meddelanden från moln till enhet.

Meddelanden från enhet till moln med IoT Hub har följande egenskaper:

* Meddelanden från enheten till molnet är hållbara och bevaras i en IoT Hubs standard **meddelanden/händelse** slut punkt i upp till sju dagar.

* Meddelanden från enheten till molnet kan vara högst 256 KB och kan grupperas i batchar för att optimera sändningar. Batchar kan vara högst 256 KB.

* IoT Hub tillåter inte godtycklig partitionering. Meddelanden från enheten till molnet partitioneras baserat på deras ursprungliga **deviceId**.

* Som förklaras i [kontrol lera åtkomsten till IoT Hub](iot-hub-devguide-security.md)kan IoT Hub aktivera autentisering per enhet och åtkomst kontroll.

* Du kan stämpla meddelanden med information som går in i program egenskaperna. Mer information finns i meddelande- [anrikninger](iot-hub-message-enrichments-overview.md).

Mer information om hur du kodar och avkodar meddelanden som skickas med olika protokoll finns i [Azure IoT SDK](iot-hub-devguide-sdks.md): er.

## <a name="system-properties-of-d2c-iot-hub-messages"></a>System egenskaper för **D2C** IoT Hub-meddelanden

| Egenskap | Beskrivning  |Anges användaren?|Nyckelord för </br>routnings fråga|
| --- | --- | --- | --- |
| meddelande-ID |En användardefinierad identifierare för meddelandet som används för svar på begäran-svar. Format: En Skift läges känslig sträng (upp till 128 tecken lång) av ASCII 7-bitars alfanumeriska `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`tecken +.  | Ja | messageId |
| iothub-enqueuedtime |Datum och tid då meddelandet från [enhet till molnet](iot-hub-devguide-d2c-guidance.md) togs emot av IoT Hub. | Nej | enqueuedTime |
| användar-ID |Ett ID som används för att ange ursprung för meddelanden. När meddelanden genereras av IoT Hub har den angetts till `{iot hub name}`. | Ja | userId |
| iothub-Connection-Device-ID |Ett ID som anges av IoT Hub för meddelanden från enhet till moln. Den innehåller **deviceId** för enheten som skickade meddelandet. | Nej | connectionDeviceId |
| iothub-Connection-module-ID |Ett ID som anges av IoT Hub för meddelanden från enhet till moln. Den innehåller **moduleId** för enheten som skickade meddelandet. | Nej | connectionModuleId |
| iothub-Connection-auth-generation-ID |Ett ID som anges av IoT Hub för meddelanden från enhet till moln. Den innehåller **connectionDeviceGenerationId** (som per [enhets identitets egenskaper](iot-hub-devguide-identity-registry.md#device-identity-properties)) för enheten som skickade meddelandet. | Nej |connectionDeviceGenerationId |
| iothub-connection-auth-method |En autentiseringsmetod som anges av IoT Hub för meddelanden från enhet till moln. Den här egenskapen innehåller information om autentiseringsmetoden som används för att autentisera enheten som skickar meddelandet.| Nej | connectionAuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>System egenskaper för **C2D** IoT Hub-meddelanden

| Egenskap | Beskrivning  |Anges användaren?|
| --- | --- | --- |
| meddelande-ID |En användardefinierad identifierare för meddelandet som används för svar på begäran-svar. Format: En Skift läges känslig sträng (upp till 128 tecken lång) av ASCII 7-bitars alfanumeriska `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`tecken +.  |Ja|
| sekvens-nummer |Ett tal (unikt per enhets kön) som tilldelas av IoT Hub till varje moln-till-enhet-meddelande. |Nej|
| till |Ett mål som anges i meddelanden från [moln till enhet](iot-hub-devguide-c2d-guidance.md) . |Nej|
| absolute-expiry-time |Datum och tid då meddelandet upphör att gälla. |Nej|   |
| korrelations-ID |En sträng egenskap i ett svarsmeddelande som vanligt vis innehåller MessageId för begäran, i mönster för begäran-svar. |Ja|
| användar-ID |Ett ID som används för att ange ursprung för meddelanden. När meddelanden genereras av IoT Hub har den angetts till `{iot hub name}`. |Ja|
| iothub-ack |En generator för feedback-meddelande. Den här egenskapen används i meddelanden från molnet till enheten för att begära IoT Hub att generera återkopplings meddelanden som ett resultat av att meddelandet har använts av enheten. Möjliga värden: **ingen** (standard): inget feedback-meddelande har skapats, **positivt**: ta emot ett feedback-meddelande om meddelandet har slutförts, **negativt**: ta emot ett feedback-meddelande om meddelandet har upphört att gälla (eller maximalt antal leveranser nått) utan att ha slutförts av enheten eller **fullständig**: både positiv och negativ. |Ja|

## <a name="message-size"></a>Meddelandestorlek

IoT Hub mäter meddelande storleken på ett oberoende sätt, överväger bara den faktiska nytto lasten. Storleken i byte beräknas som summan av följande värden:

* Text storleken i byte.
* Storleken i byte på alla värden i meddelande systemets egenskaper.
* Storlek i byte för alla användar egenskaps namn och värden.

Egenskaps namn och värden är begränsade till ASCII-tecken, så längden på strängarna motsvarar storleken i byte.

## <a name="anti-spoofing-properties"></a>Egenskaper för skydd mot förfalskning

För att undvika enhets förfalskning i meddelanden från enhet till moln, IoT Hub stämplar alla meddelanden med följande egenskaper:

* **iothub-Connection-Device-ID**
* **iothub-Connection-auth-generation-ID**
* **iothub-connection-auth-method**

De två första innehåller den ursprungliga enhetens **deviceId** och **generationId** , som per enhets [identitets egenskaper](iot-hub-devguide-identity-registry.md#device-identity-properties).

Egenskapen **iothub-Connection-auth-Method** innehåller ett JSON-serialiserat objekt med följande egenskaper:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Nästa steg

* Information om storleks gränser för meddelanden i IoT Hub finns i [IoT Hub kvoter och begränsning](iot-hub-devguide-quotas-throttling.md).

* Information om hur du skapar och läser IoT Hub meddelanden i olika programmeringsspråk finns i [snabb starter](quickstart-send-telemetry-node.md).
