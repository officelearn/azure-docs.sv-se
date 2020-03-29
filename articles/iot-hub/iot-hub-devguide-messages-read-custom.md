---
title: Förstå anpassade slutpunkter för Azure IoT Hub | Microsoft-dokument
description: Utvecklarguide – med routningsfrågor för att dirigera meddelanden från enhet till moln till anpassade slutpunkter.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61244352"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Använda meddelandevägar och anpassade slutpunkter för meddelanden från enhet till moln

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [Message Routing](iot-hub-devguide-routing-query-syntax.md) gör det möjligt för användare att dirigera meddelanden från enhet till moln till tjänstinriktade slutpunkter. Routning ger också en frågefunktion för att filtrera data innan du dirigerar dem till slutpunkterna. Varje routningsfråga som du konfigurerar har följande egenskaper:

| Egenskap      | Beskrivning |
| ------------- | ----------- |
| **Namn**      | Det unika namn som identifierar frågan. |
| **Källkod**    | Ursprunget till den dataström som ska följas. Till exempel enhetstelemetri. |
| **Villkor** | Frågeuttrycket för routningsfrågan som körs mot egenskaperna för meddelandeprogrammet, systemegenskaper, meddelandetext, enhetstvillingtaggar och enhetstvillingegenskaper för att avgöra om det är en matchning för slutpunkten. Mer information om hur du skapar en fråga finns i [syntaxen för meddelanderoutningsfrågefördirigering](iot-hub-devguide-routing-query-syntax.md) |
| **Slutpunkt**  | Namnet på slutpunkten där IoT Hub skickar meddelanden som matchar frågan. Vi rekommenderar att du väljer en slutpunkt i samma region som din IoT-hubb. |

Ett enda meddelande kan matcha villkoret för flera routningsfrågor, i vilket fall IoT Hub levererar meddelandet till slutpunkten som är associerad med varje matchad fråga. IoT Hub deduplicerar också automatiskt meddelandeleverans, så om ett meddelande matchar flera frågor som har samma mål skrivs det bara en gång till det målet.

## <a name="endpoints-and-routing"></a>Slutpunkter och routning

En IoT-hubb har en [inbyggd standardslutpunkt](iot-hub-devguide-messages-read-builtin.md). Du kan skapa anpassade slutpunkter för att dirigera meddelanden till genom att länka andra tjänster i din prenumeration till navet. IoT Hub stöder för närvarande Azure Storage-behållare, eventhubbar, servicebussköer och servicebussavsnitt som anpassade slutpunkter.

När du använder routning och anpassade slutpunkter levereras meddelanden endast till den inbyggda slutpunkten om de inte matchar någon fråga. Om du vill leverera meddelanden till den inbyggda slutpunkten och till en anpassad slutpunkt lägger du till en väg som skickar meddelanden till slutpunkten för inbyggda **händelser.**

> [!NOTE]
> * IoT Hub stöder endast att skriva data till Azure Storage-behållare som blobbar.
> * Service Bus-köer och avsnitt med **Sessioner** eller **Dubblettidentifiering** aktiverade stöds inte som anpassade slutpunkter.

Mer information om hur du skapar anpassade slutpunkter i IoT Hub finns i [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md).

Mer information om hur du läser från anpassade slutpunkter finns i:

* Läser från [Azure Storage-behållare](../storage/blobs/storage-blobs-introduction.md).

* Läser från [eventhubbar](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Läser från [servicebussköer](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Läsa från [Service Bus ämnen](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om IoT Hub-slutpunkter finns i [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md).

* Mer information om det frågespråk som du använder för att definiera routningsfrågor finns i [frågasyntax för fråga för meddelanderoutning](iot-hub-devguide-routing-query-syntax.md).

* [Process IoT Hub-meddelanden från enhet till moln med hjälp av](tutorial-routing.md) vägsjälvstudier visar hur du använder routningsfrågor och anpassade slutpunkter.