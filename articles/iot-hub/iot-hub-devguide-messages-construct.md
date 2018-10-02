---
title: Förstå Azure IoT Hub-meddelandeformat | Microsoft Docs
description: Utvecklarguide – beskrivs formatet och förväntade innehållet i meddelanden från IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 168f78ed2236d70a37a111c628e45e7bf0bbfa4b
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585415"
---
# <a name="create-and-read-iot-hub-messages"></a>Skapa och läsa IoT Hub-meddelanden

Om du vill ha stöd för smidig samverkan mellan protokoll, definierar IoT Hub ett vanliga meddelandeformat för alla protokoll för webbservergrupper på enheten. Meddelanden i formatet används för både [enhet till moln routning](iot-hub-devguide-messages-d2c.md) och [moln till enhet](iot-hub-devguide-messages-c2d.md) meddelanden. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementerar enhet-till-moln-meddelanden med hjälp av en strömmande meddelandemönster. Meddelanden från IoT Hub-enhet till molnet är mer som [Händelsehubbar](/azure/event-hubs/) *händelser* än [Service Bus](/azure/service-bus-messaging/) *meddelanden* eftersom det finns en stor volym händelser som passerar genom den tjänst som kan läsas av flera läsare.

En IoT Hub-meddelande består av:

* En förutbestämd uppsättning *Systemegenskaper* enligt nedan.

* En uppsättning *programegenskaper*. En ordlista med egenskaper för anslutningssträngar som programmet kan definiera och åtkomst, utan att behöva deserialisera meddelandetexten. IoT Hub ändrar aldrig de här egenskaperna.

* En täckande binära brödtext.

Egenskapsnamn och värden får bara innehålla alfanumeriska ASCII-tecken, plus ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` när du skickar meddelanden från enheten till molnet med hjälp av HTTPS-protokollet eller skicka meddelanden från molnet till enheten.

Meddelanden med IoT Hub enhet-till-molnet har följande egenskaper:

* Meddelanden från enheten till molnet är hållbar och sparade i en IoT-hubb standard **meddelanden/händelser** slutpunkt för upp till sju dagar.

* Meddelanden från enheten till molnet får innehålla högst 256 KB och kan grupperas i batchar att optimera skickar. Batchar får innehålla högst 256 KB.

* IoT Hub tillåter inte godtyckliga partitionering. Meddelanden från enheten till molnet partitioneras baserat på deras ursprung **deviceId**.

* Enligt beskrivningen i [styra åtkomsten till IoT Hub](iot-hub-devguide-security.md), IoT Hub kan per enhet autentisering och åtkomstkontroll.

Läs mer om hur du kodar och avkodar meddelandena som skickas med olika protokoll, [Azure IoT SDK: er](iot-hub-devguide-sdks.md).

I följande tabell visar uppsättningen Systemegenskaper i IoT Hub-meddelanden.

| Egenskap  | Beskrivning | Är användare inställbar? |
| --- | --- | --- |
| MessageId |En användare inställbar identifierare för meddelandet som används för begäran / svar-mönster. Format: En skiftlägeskänslig sträng (upp till 128 tecken) med ASCII 7 bitar alfanumeriska tecken + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Ja |
| Sekvensnummer |Ett tal (unika per enhet kö) IoT Hub har tilldelats varje moln-till-enhet-meddelande. | Nej för C2D-meddelanden. Ja annars. |
| Till |Ett mål som anges i [moln till enhet](iot-hub-devguide-c2d-guidance.md) meddelanden. | Nej för C2D-meddelanden. Ja annars. |
| ExpiryTimeUtc |Datum och tid för förfallodatum för meddelanden. | Ja |
| EnqueuedTime |Datum och tid i [moln till enhet](iot-hub-devguide-c2d-guidance.md) meddelande togs emot av IoT Hub. | Nej för C2D-meddelanden. Ja annars. |
| CorrelationId |En strängegenskap i ett svarsmeddelande som vanligtvis innehåller meddelande-ID för begäran i begäran / svar-mönster. | Ja |
| UserId |Ett ID som används för att ange ursprunget av meddelanden. När meddelanden genereras av IoT Hub, den är inställd på `{iot hub name}`. | Nej |
| Ack |En feedback meddelande generator. Den här egenskapen används i meddelanden från moln till enhet för att begära IoT Hub för att generera meddelanden på grund av användningen av meddelandet av enheten. Möjliga värden: **ingen** (standard): ingen feedback meddelande genereras **positivt**: meddelandet feedback om meddelandet har slutförts, **negativt**: ta emot en feedback-meddelande om meddelandet har gått ut (eller har nått maximalt antal leveranser) utan håller på att slutföras av enheten, eller **fullständig**: både positiva och negativa. 
<!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | Ja |
| ConnectionDeviceId |Ett ID som angetts av IoT Hub på meddelanden från enheten till molnet. Den innehåller den **deviceId** på den enhet som skickade meddelandet. | Nej för D2C-meddelanden. Ja annars. |
| ConnectionDeviceGenerationId |Ett ID som angetts av IoT Hub på meddelanden från enheten till molnet. Den innehåller den **generationId** (enligt [identitet enhetsegenskaper](iot-hub-devguide-identity-registry.md#device-identity-properties)) på den enhet som skickade meddelandet. | Nej för D2C-meddelanden. Ja annars. |
| ConnectionAuthMethod |En autentiseringsmetod som angetts av IoT Hub på meddelanden från enheten till molnet. Den här egenskapen innehåller information om den autentiseringsmetod som används för att autentisera den enhet som skickar meddelandet. <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | Nej för D2C-meddelanden. Ja annars. |
| CreationTimeUtc | Datum och tidpunkt som meddelandet skapades på en enhet. En enhet måste ange ett explicit värde. | Ja |

## <a name="message-size"></a>Meddelandestorlek

IoT Hub mäter meddelandestorlek på ett protokoll-oberoende sätt, överväger bara den faktiska nyttolasten. Storlek i byte beräknas som summan av följande:

* Brödtext storlek i byte.
* Storlek i byte för alla värden i systemet meddelandeegenskaper.
* Storlek i byte för alla användare egenskapsnamn och värden.

Egenskapsnamn och värden är begränsade till ASCII-tecken så lång strängarna är lika storlek i byte.

## <a name="anti-spoofing-properties"></a>Egenskaper för skydd mot förfalskning

För att undvika enheten förfalskning i meddelanden från enheten till molnet, IoT Hub stämplar alla meddelanden med följande egenskaper:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

De första två innehåller den **deviceId** och **generationId** för den ursprungliga enheten enligt [identitet enhetsegenskaper](iot-hub-devguide-identity-registry.md#device-identity-properties).

Den **ConnectionAuthMethod** egenskapen innehåller ett JSON-serialiserat objekt med följande egenskaper:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Nästa steg

* Information om begränsningar för meddelandestorlek i IoT Hub finns i [IoT Hub-kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md).

* Information om hur du skapar och läsa IoT Hub-meddelanden i olika programmeringsspråk, finns det [Snabbstarter](quickstart-send-telemetry-node.md).