---
title: Förstå Azure IoT Hub anpassade slutpunkter | Microsoft Docs
description: Utvecklarguide – använder Routning frågor för att dirigera meddelanden från enheten till molnet till anpassade slutpunkter.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: af0b819c6c60835089c174a1f9f7c3a6215e362c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956975"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Använd meddelandevägar och anpassade slutpunkter för meddelanden från enheten till molnet

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [meddelanderoutning](iot-hub-devguide-routing-query-syntax.md) gör att användarna kan dirigera meddelanden från enheten till molnet till tjänst-slutpunkter. Routning innehåller också en förfrågningar till möjlighet att filtrera data innan du skicka det till slutpunkterna. Varje routning fråga som du konfigurerar har följande egenskaper:

| Egenskap       | Beskrivning |
| ------------- | ----------- |
| **Namn**      | Det unika namnet som identifierar frågan. |
| **Källa**    | Ursprunget för dataström som ska åtgärdas. Till exempel enhetstelemetri. |
| **villkor** | Frågeuttryck för routning frågan som körs mot meddelande programegenskaper, Systemegenskaper, meddelandetexten, device twin taggar och tvillingegenskaper att avgöra om det finns en matchning för slutpunkten. Mer information om hur du skapar en fråga finns i [meddelande frågesyntax för Routning](iot-hub-devguide-routing-query-syntax.md) |
| **Slutpunkt**  | Namnet på slutpunkten där IoT Hub skickar meddelanden som matchar frågan. Vi rekommenderar att du väljer en slutpunkt i samma region som din IoT-hubb. |

Ett enskilt meddelande som kan matcha tillståndet på flera routning frågor om IoT-hubb levererar meddelandet till slutpunkten som är associerade med varje matchade fråga. IoT Hub automatiskt deduplicates meddelandeleverans, så om ett meddelande matchar flera frågor som har samma mål, de endast skrivs en gång till destinationen.

## <a name="endpoints-and-routing"></a>Slutpunkter och Routning

En IoT-hubb har en standard [inbyggd slutpunkt][lnk-built-in]. Du kan skapa anpassade slutpunkter skicka meddelanden till genom att länka andra tjänster i din prenumeration till hubben. IoT Hub stöder för närvarande Azure Storage-behållare, Event Hubs, Service Bus-köer och Service Bus-ämnen som anpassade slutpunkter.

När du använder Routning och anpassade slutpunkter levereras endast meddelanden till den inbyggda slutpunkten om de inte matchar alla frågor. Om du vill skicka meddelanden till den inbyggda slutpunkten även om en anpassad slutpunkt, lägger du till ett flöde som skickar meddelanden till den **händelser** slutpunkt.

> [!NOTE]
> IoT Hub stöder endast skriva data till Azure Storage-behållare som blobar.

> [!WARNING]
> Service Bus-köer och ämnen med **sessioner** eller **dubblettidentifiering** aktiverat stöds inte som anpassade slutpunkter.

Mer information om hur du skapar anpassade slutpunkter i IoT Hub finns i [IoT Hub-slutpunkter][lnk-devguide-endpoints].

Mer information om läsning från anpassade slutpunkter finns:

* Läsa från [Azure Storage-behållare][lnk-getstarted-storage].
* Läsa från [Händelsehubbar][lnk-getstarted-eh].
* Läsa från [Service Bus-köer][lnk-getstarted-queue].
* Läsa från [Service Bus-ämnen][lnk-getstarted-topic].

### <a name="next-steps"></a>Nästa steg

* Läs mer om IoT Hub-slutpunkter, [IoT Hub-slutpunkter][lnk-devguide-endpoints].
* Mer information om frågespråket som du använder för att definiera routning frågor finns i [frågesyntax för routning till meddelandet](iot-hub-devguide-routing-query-syntax.md).
* Den [Process IoT Hub enhet-till-moln-meddelanden med vägar] [ lnk-d2c-tutorial] självstudiekursen visar hur du använder Routning frågor och anpassade slutpunkter.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
