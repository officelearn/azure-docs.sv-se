---
title: Förstå Azure IoT Hub-meddelandeformat | Microsoft-dokument
description: Utvecklarguide - beskriver formatet och det förväntade innehållet i IoT Hub-meddelanden.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 28537ac2389fbb1ca43ca4014515564bddeba4ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69872485"
---
# <a name="create-and-read-iot-hub-messages"></a>Skapa och läsa IoT Hub-meddelanden

För att stödja sömlös interoperabilitet mellan protokoll definierar IoT Hub ett gemensamt meddelandeformat för alla enhetsinriktade protokoll. Det här meddelandeformatet används för både [enhets-till-moln-routning](iot-hub-devguide-messages-d2c.md) och [meddelanden från molnet till enheten.](iot-hub-devguide-messages-c2d.md) 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementerar meddelanden från enhet till moln med hjälp av ett meddelandemönster för strömmande meddelanden. IoT Hub s enhet-till-moln-meddelanden är mer som [Event Hubs](/azure/event-hubs/) *händelser* än [Service Bus](/azure/service-bus-messaging/) *meddelanden* i att det finns en stor mängd händelser som passerar genom tjänsten som kan läsas av flera läsare.

Ett IoT Hub-meddelande består av:

* En förutbestämd uppsättning *systemegenskaper* som anges nedan.

* En uppsättning *programegenskaper*. En ordlista med strängegenskaper som programmet kan definiera och komma åt, utan att behöva deserialisera meddelandetexten. IoT Hub ändrar aldrig dessa egenskaper.

* En ogenomskinlig binär kropp.

Egenskapsnamn och värden kan bara innehålla ASCII-alfanumeriska tecken, plus ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` när du skickar meddelanden från enhet till moln med HTTPS-protokollet eller skickar meddelanden från molnet till enheten.

Enhets-till-moln-meddelanden med IoT Hub har följande egenskaper:

* Enhets-till-moln-meddelanden är hållbara och behålls i en IoT-hubbs standardslutpunkt för **meddelanden/händelser** i upp till sju dagar.

* Enhet-till-moln-meddelanden kan vara högst 256 KB och kan grupperas i batchar för att optimera sändningsmeddelanden. Batchar kan vara högst 256 KB.

* IoT Hub tillåter inte godtycklig partitionering. Enhet-till-moln-meddelanden partitioneras baserat på deras ursprungliga **deviceId**.

* Som förklaras i [Control-åtkomst till IoT Hub](iot-hub-devguide-security.md)möjliggör IoT Hub autentisering och åtkomstkontroll per enhet.

* Du kan stämpla meddelanden med information som går in i programegenskaperna. Mer information finns i [meddelandeberikande .](iot-hub-message-enrichments-overview.md)

Mer information om hur du kodar och avkodar meddelanden som skickas med olika protokoll finns i [Azure IoT SDK:er](iot-hub-devguide-sdks.md).

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Systemegenskaper för **D2C** IoT Hub-meddelanden

| Egenskap | Beskrivning  |Kan du använda dig?|Nyckelord för </br>routningsfråga|
| --- | --- | --- | --- |
| meddelande-id |En användaridentifierbar identifierare för meddelandet som används för mönster för begäran och svar. Format: En skiftlägeskänslig sträng (upp till 128 tecken lång) med ASCII 7-bitars alfanumeriska tecken + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  | Ja | messageId |
| iothub-enqueuedtime |Datum och tid då [meddelandet enhet till moln](iot-hub-devguide-d2c-guidance.md) togs emot av IoT Hub. | Inga | enqueuedTime |
| användar-id |Ett ID som används för att ange meddelandenas ursprung. När meddelanden genereras av IoT Hub är `{iot hub name}`det inställt på . | Ja | userId |
| iothub-anslutning-enhet-id |Ett ID som anges av IoT Hub på meddelanden från enhet till moln. Den innehåller **deviceId** för enheten som skickade meddelandet. | Inga | anslutningDeviceId |
| iothub-anslutning-modul-id |Ett ID som anges av IoT Hub på meddelanden från enhet till moln. Den innehåller **moduleId** för enheten som skickade meddelandet. | Inga | anslutningModuleId |
| iothub-connection-auth-generation-id |Ett ID som anges av IoT Hub på meddelanden från enhet till moln. Den innehåller **connectionDeviceGenerationId** (enligt [enhetsidentitetsegenskaper)](iot-hub-devguide-identity-registry.md#device-identity-properties)för enheten som skickade meddelandet. | Inga |anslutningDeviceGenerationId |
| iothub-connection-auth-metod |En autentiseringsmetod som angetts av IoT Hub på meddelanden från enhet till moln. Den här egenskapen innehåller information om den autentiseringsmetod som används för att autentisera enheten som skickar meddelandet.| Inga | anslutningAuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Systemegenskaper för **C2D** IoT Hub-meddelanden

| Egenskap | Beskrivning  |Kan du använda dig?|
| --- | --- | --- |
| meddelande-id |En användaridentifierbar identifierare för meddelandet som används för mönster för begäran och svar. Format: En skiftlägeskänslig sträng (upp till 128 tecken lång) med ASCII 7-bitars alfanumeriska tecken + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  |Ja|
| sekvensnummer |Ett nummer (unikt per enhetskön) som tilldelats av IoT Hub till varje meddelande från molnet till enheten. |Inga|
| till |En destination som anges i meddelanden från [molnet till enhet.](iot-hub-devguide-c2d-guidance.md) |Inga|
| absolut utgångstid |Datum och tid för meddelandets förfallodatum. |Inga|   |
| korrelations-id |En strängegenskap i ett svarsmeddelande som vanligtvis innehåller MessageId för begäran, i mönster för begäran-svar. |Ja|
| användar-id |Ett ID som används för att ange meddelandenas ursprung. När meddelanden genereras av IoT Hub är `{iot hub name}`det inställt på . |Ja|
| iothub-ack |En feedback meddelande generator. Den här egenskapen används i moln-till-enhet-meddelanden för att begära IoT Hub för att generera feedbackmeddelanden som ett resultat av enhetens förbrukning av meddelandet. Möjliga värden: **ingen** (standard): inget feedbackmeddelande genereras, **positivt**: få ett feedbackmeddelande om meddelandet har slutförts, **negativt**: få ett feedbackmeddelande om meddelandet har upphört att gälla (eller maximalt leveransantal uppnåddes) utan att slutföras av enheten, eller **full**: både positivt och negativt. |Ja|

## <a name="message-size"></a>Meddelandestorlek

IoT Hub mäter meddelandestorlek på ett protokolloberoende sätt, med tanke på endast den faktiska nyttolasten. Storleken i byte beräknas som summan av följande värden:

* Kroppsstorleken i byte.
* Storleken i byte för alla värden för egenskaperna för meddelandesystemet.
* Storleken i byte för alla användaregenskapsnamn och värden.

Egenskapsnamn och värden är begränsade till ASCII-tecken, så strängarnas längd är lika med storleken i byte.

## <a name="anti-spoofing-properties"></a>Anti-spoofing egenskaper

För att undvika enhetsspoofing i meddelanden mellan enheter och moln stämplar IoT Hub alla meddelanden med följande egenskaper:

* **iothub-anslutning-enhet-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-metod**

De två första innehåller **deviceId** och **generationId** för den ursprungliga enheten, enligt [Enhetsidentitetsegenskaper](iot-hub-devguide-identity-registry.md#device-identity-properties).

Egenskapen **iothub-connection-auth-method** innehåller ett JSON serialiserat objekt med följande egenskaper:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Nästa steg

* Information om storleksbegränsningar för meddelanden i IoT Hub finns i [IoT Hub-kvoter och begränsning](iot-hub-devguide-quotas-throttling.md).

* Mer information om hur du skapar och läser IoT Hub-meddelanden på olika programmeringsspråk finns i [Snabbstarten](quickstart-send-telemetry-node.md).
