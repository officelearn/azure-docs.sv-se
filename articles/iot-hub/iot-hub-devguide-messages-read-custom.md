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
ms.date: 11/29/2017
ms.author: dobett
ms.openlocfilehash: d1e22a4378caf69d2077d79f78682c4d438dbcd2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Använd meddelandevägar och anpassade slutpunkter för meddelanden från enhet till moln

IoT-hubb kan du dirigera [meddelanden från enhet till moln] [ lnk-device-to-cloud] till IoT-hubb service-riktade slutpunkter baserat på Egenskaper. Regler för routning ger dig möjlighet att skicka meddelanden om de måste gå utan att behöva ytterligare tjänster för att bearbeta meddelanden eller skriva ytterligare kod. Varje regel för vidarebefordran av du konfigurerar har följande egenskaper:

| Egenskap      | Beskrivning |
| ------------- | ----------- |
| **Namn**      | Unika namn som identifierar regeln. |
| **Källa**    | Ursprung för dataström som ska användas. Till exempel enhetstelemetrin. |
| **Villkor** | Frågeuttrycket för regel för vidarebefordran som körs mot meddelandets rubriker och brödtext och används för att avgöra om det finns en matchning för slutpunkten. Mer information om hur du skapar en väg villkor finns i [referens - frågespråket för jobb och enheten twins][lnk-devguide-query-language]. |
| **Slutpunkt**  | Namnet på slutpunkten där IoT-hubb skickar meddelanden som matchar villkoret. Slutpunkter måste vara i samma region som IoT-hubben, får annars du debiteras för cross-region skrivningar. |

Ett enda meddelande kan matchar villkoret på flera routningsregler fallet IoT-hubb skickar meddelandet till slutpunkten som är associerade med varje matchade regel. IoT-hubb deduplicates också automatiskt meddelandeleverans, så om ett meddelande matchar flera regler som alla har samma mål, skrivs den bara till denna destination en gång.

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
