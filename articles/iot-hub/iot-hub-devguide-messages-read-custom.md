---
title: "Förstå Azure IoT Hub anpassade slutpunkter | Microsoft Docs"
description: "Utvecklarhandbok - använder regler för routning för att vidarebefordra meddelanden från enhet till moln till anpassade slutpunkter."
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
ms.openlocfilehash: a40fa94260b488e9c01ac09b22da8c0677d73968
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Använd meddelandevägar och anpassade slutpunkter för meddelanden från enhet till moln

IoT-hubb kan du dirigera [meddelanden från enhet till moln] [ lnk-device-to-cloud] till IoT-hubb service-riktade slutpunkter baserat på Egenskaper. Regler för routning ger dig möjlighet att skicka meddelanden när de behöver gå utan att behöva ytterligare tjänster eller anpassad kod. Varje regel för vidarebefordran av du konfigurerar har följande egenskaper:

| Egenskap      | Beskrivning |
| ------------- | ----------- |
| **Namn**      | Unika namn som identifierar regeln. |
| **Source**    | Ursprung för dataström som ska användas. Till exempel enhetstelemetrin. |
| **Villkor** | Frågeuttrycket för regel för vidarebefordran som körs mot meddelandets sidhuvuden och brödtext och avgör om det finns en matchning för slutpunkten. Mer information om hur du skapar en väg villkor finns i [referens - frågespråket för jobb och enheten twins][lnk-devguide-query-language]. |
| **Slutpunkt**  | Namnet på slutpunkten där IoT-hubb skickar meddelanden som matchar villkoret. Slutpunkter måste vara i samma region som IoT-hubben, får annars du debiteras för cross-region skrivningar. |

Ett enda meddelande kan matchar villkoret på flera routningsregler fallet IoT-hubb skickar meddelandet till slutpunkten som är associerade med varje matchade regel. IoT-hubb också automatiskt deduplicates meddelandeleverans, så om ett meddelande matchar flera regler som har samma mål, det är bara skriva en gång till denna destination.

En IoT-hubb har standard [inbyggd slutpunkt][lnk-built-in]. Du kan skapa anpassade slutpunkter skicka meddelanden till andra tjänster i din prenumeration länkar till hubben. IoT-hubb stöder för närvarande Azure Storage-behållare, Event Hubs, Service Bus-köer och Service Bus-ämnen som anpassade slutpunkter.

När du använder Routning och anpassade slutpunkter, levereras endast meddelanden till inbyggda slutpunkten om de inte matchar alla regler. För att leverera meddelanden till inbyggda slutpunkten även om en egen slutpunkt, lägga till en väg som skickar meddelanden till den **händelser** slutpunkt.

> [!NOTE]
> IoT-hubb endast stöd för skrivning av data till Azure Storage-behållare som blobar.

> [!WARNING]
> Service Bus-köer och ämnen med **sessioner** eller **dubblettidentifiering** aktiverat som anpassade slutpunkter stöds inte.

Mer information om hur du skapar anpassade slutpunkter i IoT-hubb finns [IoT-hubbslutpunkter][lnk-devguide-endpoints].

För mer information om läsning från anpassade slutpunkter, se:

* Läsning från [Azure Storage-behållare][lnk-getstarted-storage].
* Läsning från [Händelsehubbar][lnk-getstarted-eh].
* Läsning från [Service Bus-köer][lnk-getstarted-queue].
* Läsning från [Service Bus-ämnen][lnk-getstarted-topic].

### <a name="next-steps"></a>Nästa steg

Läs mer om IoT-hubbslutpunkter [IoT-hubbslutpunkter][lnk-devguide-endpoints].

Mer information om frågespråket som du använder för att definiera regler för routning finns [IoT-hubb frågespråk för enheten twins, jobb och meddelanderoutning][lnk-devguide-query-language].

Den [processen IoT Hub-enhet till moln meddelanden med hjälp av vägar] [ lnk-d2c-tutorial] kursen visar hur du använder regler för Routning och anpassade slutpunkter.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
