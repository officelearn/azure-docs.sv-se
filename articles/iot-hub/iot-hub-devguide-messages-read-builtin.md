---
title: Förstå den inbyggda slutpunkten för Azure IoT Hub | Microsoft-dokument
description: Utvecklarguide – beskriver hur du använder den inbyggda, Event Hub-kompatibla slutpunkten för att läsa meddelanden från enhet till moln.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom: amqp
ms.openlocfilehash: 169d926e466559bc83ba64ce9e976e0d725f614d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729988"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Läsa meddelanden från enhet till moln från den inbyggda slutpunkten

Som standard dirigeras meddelanden till den inbyggda tjänstinriktade slutpunkten **(meddelanden/händelser)** som är kompatibel med [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Den här slutpunkten visas för närvarande endast med [AMQP-protokollet](https://www.amqp.org/) på port 5671. En IoT-hubb visar följande egenskaper så att du kan styra de inbyggda händelsehubbkompatibla **slutpunktsmeddelanden/-händelser**som är kompatibla .

| Egenskap            | Beskrivning |
| ------------------- | ----------- |
| **Antal partitioner** | Ange den här egenskapen när du skapar för att definiera antalet [partitioner](../event-hubs/event-hubs-features.md#partitions) för händelsematning från enhet till moln. |
| **Kvarhållningstid**  | Den här egenskapen anger hur länge i dagar meddelanden behålls av IoT Hub. Standard är en dag, men det kan ökas till sju dagar. |

IoT Hub tillåter datalagring i de inbyggda eventhubbar i högst 7 dagar. Du kan ställa in kvarhållningstiden när du skapar din IoT Hub. Datalagringstiden i IoT Hub beror på din IoT-hubbnivå och enhetstyp. När det gäller storlek kan de inbyggda händelsehubbar behålla meddelanden med den maximala meddelandestorleken upp till minst 24 timmars kvot. Till exempel för 1 S1 enhet IoT Hub ger tillräckligt med lagringsutrymme för att behålla minst 400K meddelanden med 4k storlek vardera. Om dina enheter skickar mindre meddelanden kan de behållas längre (upp till 7 dagar) beroende på hur mycket lagringsutrymme som förbrukas. Vi garanterar att uppgifterna behålls för den angivna lagringstiden som ett minimum. Meddelanden upphör att gälla och kommer inte att vara tillgängliga när kvarhållningstiden har passerat. 

IoT Hub kan du också hantera konsumentgrupper på den inbyggda slutpunkten för mottagning av enhet till moln. Du kan ha upp till 20 konsumentgrupper för varje IoT Hub.

Om du använder [meddelanderoutning](iot-hub-devguide-messages-d2c.md) och [reservvägen](iot-hub-devguide-messages-d2c.md#fallback-route) är aktiverad går alla meddelanden som inte matchar en fråga på någon väg till den inbyggda slutpunkten. Om du inaktiverar den här reservvägen tas meddelanden som inte matchar någon fråga bort.

Du kan ändra kvarhållningstiden, antingen programmässigt med hjälp av [IoT Hub-resursprovidern REST API:er](/rest/api/iothub/iothubresource)eller med [Azure-portalen](https://portal.azure.com).

IoT Hub visar de **inbyggda meddelanden/händelser** som den inbyggda slutpunkten för backend-tjänster för att läsa de meddelanden som tas emot av din hubb. Den här slutpunkten är Event Hub-kompatibel, vilket gör att du kan använda någon av de mekanismer som tjänsten Event Hubs stöder för att läsa meddelanden.

## <a name="read-from-the-built-in-endpoint"></a>Läs från den inbyggda slutpunkten

Vissa produktintegreringar och eventhubbar SDK:er är medvetna om IoT Hub och låter dig använda anslutningssträngen för IoT-hubbtjänsten för att ansluta till den inbyggda slutpunkten.

När du använder SDK:er för händelsehubbar eller produktintegreringar som inte känner till IoT Hub behöver du en eventnana-kompatibel slutpunkt och händelsehubbkompatibelt namn. Du kan hämta dessa värden från portalen på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.

2. Klicka på **Inbyggda slutpunkter**.

3. Avsnittet **Händelser** innehåller följande värden: **Partitioner**, **Event Hub-kompatibelt namn,** **Event Hub-kompatibel slutpunkt,** **Kvarhållningstid**och **Konsumentgrupper**.

    ![Inställningar för enhet till moln](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

I portalen innehåller fältet Händelsenah-kompatibel slutpunkt en komplett anslutningssträng för händelsehubbar som ser ut så här: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/; SharedAccessKeyName=iothubowner; SharedAccessKeyName=iothubowner; SharedAccessKeyName=iothubowner; SharedAccess SharedAccessKey=keykeykeykeykeykeykey=; EntityPath=iothub-ehub-abcd-1234-123456**. Om SDK du använder kräver andra värden, då de skulle vara:

| Namn | Värde |
| ---- | ----- |
| Slutpunkt | sb://abcd1234namespace.servicebus.windows.net/ |
| Värdnamn | abcd1234namespace.servicebus.windows.net |
| Namnområde | abcd1234namespace |

Du kan sedan använda alla principer för delad åtkomst som har **ServiceConnect-behörigheterna** för att ansluta till den angivna händelsehubben.

De SDK:er som du kan använda för att ansluta till den inbyggda händelsenav-kompatibla slutpunkten som IoT Hub exponerar är:

| Språk | SDK | Exempel | Anteckningar |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Snabbstart](quickstart-send-telemetry-dotnet.md) | Använder information om händelsehubbar |
 Java | https://github.com/Azure/azure-event-hubs-java | [Snabbstart](quickstart-send-telemetry-java.md) | Använder information om händelsehubbar |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Snabbstart](quickstart-send-telemetry-node.md) | Använder anslutningssträng för IoT Hub |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Använder anslutningssträng för IoT Hub |

De produktintegreringar som du kan använda med den inbyggda händelsenavet-kompatibla slutpunkten som IoT Hub exponerar inkluderar:

* [Azure-funktioner](https://docs.microsoft.com/azure/azure-functions/). Se [Bearbeta data från IoT Hub med Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Se [Strömma data som indata i Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Insikter i tidsserier](https://docs.microsoft.com/azure/time-series-insights/). Se [Lägga till en IoT-hubbhändelsekälla i time series insights-miljön](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Apache Storm pip](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Du kan visa [pipkällan](https://github.com/apache/storm/tree/master/external/storm-eventhubs) på GitHub.
* [Apache Spark integration](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Nästa steg

* Mer information om IoT Hub-slutpunkter finns i [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md).

* [Snabbstartarna](quickstart-send-telemetry-node.md) visar hur du skickar meddelanden från enheter till molnet från simulerade enheter och läser meddelandena från den inbyggda slutpunkten. 

Mer information finns i [process-IoT Hub-meddelanden från enhet till moln med hjälp av](tutorial-routing.md) självstudiekurs för vägar.

* Om du vill dirigera dina meddelanden från enhet till moln till anpassade slutpunkter läser [du Använda meddelandevägar och anpassade slutpunkter för meddelanden från enhet till moln](iot-hub-devguide-messages-read-custom.md).
