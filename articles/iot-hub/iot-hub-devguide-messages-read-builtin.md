---
title: "Förstå Azure IoT Hub inbyggd slutpunkt | Microsoft Docs"
description: "Utvecklarhandbok - beskriver hur du använder inbyggda, Event Hub-kompatibel endpoint toread enhet till moln meddelanden."
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
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: c9e6aa03e3a1e0592223630c7b81634bcb09add6
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Läsa meddelanden från enhet till moln från inbyggd slutpunkt

Som standard meddelanden dirigeras till den inbyggda tjänst-riktade slutpunkten (**meddelanden/händelser**) som är kompatibel med [Händelsehubbar][lnk-event-hubs]. Den här slutpunkten är bara synliga använder den [AMQP] [ lnk-amqp] -protokollet på port 5671. En IoT-hubb exponerar följande egenskaper så att du kan styra inbyggda Event Hub-kompatibel meddelanden slutpunkten **meddelanden/händelser**.

| Egenskap            | Beskrivning |
| ------------------- | ----------- |
| **Partitionsantal** | Ange den här egenskapen när skapas för att definiera antalet [partitioner] [ lnk-event-hub-partitions] för enhet till moln händelse införandet. |
| **Kvarhållningstid**  | Den här egenskapen anger hur länge i dagar meddelanden finns kvar i IoT Hub. Standardvärdet är en dag, men det kan ökas till sju dagar. |

IoT-hubb kan du hantera konsumentgrupper på det inbyggda enhet till molnet får slutpunkt.

Som standard skrivs alla meddelanden som inte uttryckligen matchar en regel för routning av meddelanden till inbyggd slutpunkt. Om du inaktiverar den här återställningsplats vägen släpps meddelanden som inte uttryckligen matchar alla regler för routning av meddelandet.

Du kan ändra tiden för datakvarhållning antingen programmässigt via den [IoT-hubb resursprovidern REST API: er][lnk-resource-provider-apis], eller genom att använda den [Azure-portalen][lnk-management-portal].

IoT-hubb visar den **meddelanden/händelser** inbyggd slutpunkt för dina backend-tjänster att läsa de enhet till moln har tagits emot av din hubb. Den här slutpunkten är Event Hub-kompatibel, där du kan använda någon av av mekanismer händelsehubbtjänsten har stöd för att läsa meddelanden.

## <a name="read-from-the-built-in-endpoint"></a>Läsa från inbyggd slutpunkt

När du använder den [Azure Service Bus SDK för .NET] [ lnk-servicebus-sdk] eller [Händelsehubbar - värd för händelsebearbetning][lnk-eventprocessorhost], du kan använda alla IoT-hubb anslutningssträngar med rätt behörighet. Använd sedan **meddelanden/händelser** som Event Hub-namn.

När du använder SDK: er (eller produkten integreringar) som inte känner till IoT-hubb, du måste hämta en Event Hub-kompatibel slutpunkt och Event Hub-kompatibelt namn från IoT-hubbsinställningar:

1. Logga in på den [Azure-portalen] [ lnk-management-portal] och navigera till din IoT-hubb.
1. Klicka på **Slutpunkter**.
1. I den **inbyggda slutpunkter** klickar du på **händelser**. 
1. Öppnar sidan med egenskaper, som innehåller följande värden: **Event Hub-kompatibel endpoint**, **Event Hub-kompatibelt namn**, **partitioner**,  **Kvarhållningstiden**, och **konsumentgrupper**.

    ![Inställningar för enhet till moln][img-eventhubcompatible]

IoT-hubb SDK kräver slutpunktsnamnet IoT-hubb som är **meddelanden/händelser** som visas **slutpunkter**.

Om du använder SDK kräver en **värdnamn** eller **Namespace** värde, ta bort schemat från den **Event Hub-kompatibel endpoint**. Om din Event Hub-kompatibel slutpunkten är till exempel **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, **värdnamn** skulle vara  **iothub-ns-myiothub-1234.servicebus.windows.net**. Den **Namespace** skulle vara **iothub-ns-myiothub-1234**.

Du kan sedan använda en princip för delad åtkomst som har den **ServiceConnect** behörighet att ansluta till den angivna Event Hub.

Om du behöver för att skapa en Event Hub-anslutningssträngen med hjälp av föregående informationen, använder du följande mönster:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

SDK: er och integreringar som du kan använda med Event Hub-kompatibel slutpunkter som IoT-hubb exponerar innehåller objekt i listan nedan:

* [Java händelsehubbklient](https://github.com/Azure/azure-event-hubs-java).
* [Apache Storm-kanal](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Du kan visa den [prata källa](https://github.com/apache/storm/tree/master/external/storm-eventhubs) på GitHub.
* [Apache Spark integration](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om IoT-hubbslutpunkter [IoT-hubbslutpunkter][lnk-endpoints].

Den [Kom igång] [ lnk-get-started] självstudiekurser visar hur du skickar meddelanden från enhet till moln från simulerade enheter och läsa meddelanden från den inbyggda slutpunkten. Mer information finns i [processen IoT Hub-enhet till moln meddelanden med hjälp av vägar] [ lnk-d2c-tutorial] kursen.

Om du vill dirigera dina meddelanden från enhet till moln till anpassade slutpunkter, se [använder meddelandevägar och anpassade slutpunkter för meddelanden från enhet till moln][lnk-custom].

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-amqp]: https://www.amqp.org/
