---
title: Förstå den inbyggda slutpunkten i Azure IoT Hub | Microsoft Docs
description: Utvecklarguide – beskriver hur du använder inbyggda, Event Hubs-kompatibla slutpunkten toread enhet till moln meddelanden.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 767c91e4926e553b63b8331ac99edcd7823d2c13
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055023"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Läsa meddelanden från enhet till moln från den inbyggda slutpunkten

Som standard dirigeras meddelanden till den inbyggda tjänst-riktade slutpunkten (**meddelanden/händelser**) som är kompatibel med [Händelsehubbar][lnk-event-hubs]. Den här slutpunkten är för närvarande endast synliga använder den [AMQP] [ lnk-amqp] -protokollet på port 5671. En IoT hub exponerar följande egenskaper så att du kan styra den inbyggda meddelanden för Event Hub-kompatibla slutpunkten **meddelanden/händelser**.

| Egenskap             | Beskrivning |
| ------------------- | ----------- |
| **Antalet partitioner** | Ange den här egenskapen när du skapar för att definiera hur många [partitioner] [ lnk-event-hub-partitions] för enhet-till-moln händelsepåfyllning. |
| **Kvarhållningstid**  | Den här egenskapen anger hur länge i dagar som meddelanden ska bevaras av IoT Hub. Standardvärdet är en dag, men det kan ökas till sju dagar. |

IoT Hub hjälper dig att hantera konsumentgrupper på det inbyggda enhet till molnet får slutpunkt.

Som standard skrivs alla meddelanden som inte uttryckligen matchar en regel för vidarebefordran av meddelande till den inbyggda slutpunkten. Om du inaktiverar den här vägen som återställningsplats, hamnar meddelanden som inte matchar några regler för meddelandedirigering uttryckligen.

Du kan ändra kvarhållningstiden, antingen via programmering med hjälp av den [resursprovidern i IoT Hub REST API: er][lnk-resource-provider-apis], eller med den [Azure-portalen] [ lnk-management-portal].

IoT-hubb exponerar den **meddelanden/händelser** inbyggd slutpunkt för ditt backend-tjänster kan läsa enhet-till-moln-meddelanden som tas emot av hubben. Den här slutpunkten är Event Hubs-kompatibla, där du kan använda någon av mekanismerna Event Hubs-tjänsten har stöd för att läsa meddelanden.

## <a name="read-from-the-built-in-endpoint"></a>Läsa från den inbyggda slutpunkten

När du använder den [Azure Service Bus SDK för .NET] [ lnk-servicebus-sdk] eller [Event Hubs – Eventprocessorhost][lnk-eventprocessorhost], du kan använda en IoT Hub-anslutning strängar med rätt behörighet. Använd sedan **meddelanden/händelser** som Event Hub-namn.

När du använder SDK: er (eller produktintegreringar) som inte känner till IoT Hub, måste du hämta en Event Hub-kompatibla slutpunkten och Händelsehubb-kompatibelt namn:

1. Logga in på den [Azure-portalen] [ lnk-management-portal] och navigera till din IoT-hubb.
1. Klicka på **Slutpunkter**.
1. I den **inbyggda slutpunkter** klickar du på **händelser**. 
1. En egenskapssida öppnas som innehåller följande värden: **Event Hub-kompatibla slutpunkten**, **Event Hub-kompatibla namnet**, **partitioner**,  **Kvarhållningstid**, och **konsumentgrupper**.

    ![Inställningar för enhet till moln][img-eventhubcompatible]

IoT Hub SDK kräver slutpunktsnamn IoT Hub som är **meddelanden/händelser** som visas **slutpunkter**.

Om du använder SDK: N kräver en **värdnamn** eller **Namespace** värde, ta bort schemat från den **Event Hub-kompatibla slutpunkten**. Exempel: om din Event Hub-kompatibla slutpunkten är **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, **värdnamn** skulle vara  **iothub-ns-myiothub-1234.servicebus.windows.net**. Den **Namespace** skulle vara **iothub-ns-myiothub-1234**.

Du kan sedan använda någon princip för delad åtkomst som har den **ServiceConnect** behörighet att ansluta till den angivna Event Hub.

Om du vill skapa en Event Hub-anslutningssträng med hjälp av ovanstående information kan du använda följande mönster:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

SDK: er och integreringar som du kan använda med Event Hubs-kompatibla slutpunkter som IoT-hubb exponerar innehåller objekten i listan nedan:

* [Java händelsehubbklient](https://github.com/Azure/azure-event-hubs-java).
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Du kan visa den [käll-kanalen](https://github.com/apache/storm/tree/master/external/storm-eventhubs) på GitHub.
* [Integration av Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om IoT Hub-slutpunkter, [IoT Hub-slutpunkter][lnk-endpoints].

Den [Snabbstarter] [ lnk-get-started] visar hur du skickar meddelanden från enheten till molnet från simulerade enheter och läsa meddelanden från den inbyggda slutpunkten. Mer information finns i den [Process IoT Hub enhet-till-moln-meddelanden med vägar] [ lnk-d2c-tutorial] självstudien.

Om du vill dirigera din enhet till moln-meddelanden till anpassade slutpunkter finns i [använder meddelandevägar och anpassade slutpunkter för meddelanden från enheten till molnet][lnk-custom].

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph
[lnk-amqp]: https://www.amqp.org/
