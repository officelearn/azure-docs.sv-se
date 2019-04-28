---
title: Förstå den inbyggda slutpunkten i Azure IoT Hub | Microsoft Docs
description: Utvecklarguide – beskriver hur du använder den inbyggda, Event Hubs-kompatibla slutpunkten kan läsa meddelanden från enhet till molnet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 827d7d9a3d584342703a84dd2a42e5cda9b3a656
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364070"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Läsa meddelanden från enhet till moln från den inbyggda slutpunkten

Som standard dirigeras meddelanden till den inbyggda tjänst-riktade slutpunkten (**meddelanden/händelser**) som är kompatibel med [Händelsehubbar](https://azure.microsoft.com/documentation/services/event-hubs/). Den här slutpunkten är för närvarande endast synliga använder den [AMQP](https://www.amqp.org/) -protokollet på port 5671. En IoT hub exponerar följande egenskaper så att du kan styra den inbyggda meddelanden för Event Hub-kompatibla slutpunkten **meddelanden/händelser**.

| Egenskap             | Beskrivning |
| ------------------- | ----------- |
| **Antalet partitioner** | Ange den här egenskapen när du skapar för att definiera hur många [partitioner](../event-hubs/event-hubs-features.md#partitions) för enhet-till-moln händelsepåfyllning. |
| **Kvarhållningstid**  | Den här egenskapen anger hur länge i dagar som meddelanden ska bevaras av IoT Hub. Standardvärdet är en dag, men det kan ökas till sju dagar. |

IoT Hub kan kvarhållning av data i den inbyggda Händelsehubbar högst 7 dagar. Du kan ange kvarhållningstid under skapandet av din IoT-hubb. Tiden för datakvarhållning i IoT Hub beror på nivån för IoT hub och typ av enhet. När det gäller storlek, kan den inbyggda Event Hubs behåller meddelanden av den maximala meddelandestorleken upp till minst 24 timmars kvot. Till exempel för 1 S1-enhet som IoT Hub ger tillräckligt med lagringsutrymme för att behålla minst storlek 400K meddelanden med 4k var och en. Om dina enheter skickar mindre meddelanden, kan de bevaras under längre tid (upp till 7 dagar) beroende på hur mycket lagringsutrymme som förbrukas. Vi garanterar att bevara data under den angivna kvarhållningstiden minst.

IoT Hub hjälper dig att hantera konsumentgrupper på det inbyggda enhet till molnet får slutpunkt.

Om du använder [meddelanderoutning](iot-hub-devguide-messages-d2c.md) och [återställningsplats väg](iot-hub-devguide-messages-d2c.md#fallback-route) är aktiverat kan alla meddelanden som inte matchar en fråga på någon väg går du till den inbyggda slutpunkten. Om du inaktiverar den här vägen som återställningsplats, hamnar meddelanden som inte matchar alla frågor.

Du kan ändra kvarhållningstiden, antingen via programmering med hjälp av den [resursprovidern i IoT Hub REST API: er](/rest/api/iothub/iothubresource), eller med den [Azure-portalen](https://portal.azure.com).

IoT-hubb exponerar den **meddelanden/händelser** inbyggd slutpunkt för ditt backend-tjänster kan läsa enhet-till-moln-meddelanden som tas emot av hubben. Den här slutpunkten är Event Hubs-kompatibla, där du kan använda någon av mekanismerna Event Hubs-tjänsten har stöd för att läsa meddelanden.

## <a name="read-from-the-built-in-endpoint"></a>Läsa från den inbyggda slutpunkten

Vissa produktintegreringar och SDK: er för Event Hubs är medvetna om IoT Hub och du kan använda anslutningssträngen för IoT hub-tjänsten för att ansluta till den inbyggda slutpunkten.

När du använder SDK: er för Event Hubs eller produktintegreringar som inte känner till IoT Hub, behöver du en Event Hub-kompatibla slutpunkten och Händelsehubb-kompatibelt namn. Du kan hämta dessa värden från portalen enligt följande:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.

2. Klicka på **inbyggda slutpunkter**.

3. Den **händelser** avsnittet innehåller följande värden: **Partitioner**, **Event Hub-kompatibla namnet**, **Event Hub-kompatibla slutpunkten**, **kvarhållningstid**, och **konsumentgrupper**.

    ![Inställningar för enhet till moln](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

I portalen innehåller en fullständig Event Hubs-anslutningssträng som ser ut som i fältet Event Hub-kompatibla slutpunkten: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Om du använder SDK: N kräver andra värden, skulle och vara:

| Namn | Värde |
| ---- | ----- |
| Slutpunkt | sb://abcd1234namespace.servicebus.windows.net/ |
| Värdnamn | abcd1234namespace.servicebus.windows.net |
| Namnområde | abcd1234namespace |

Du kan sedan använda någon princip för delad åtkomst som har den **ServiceConnect** behörighet att ansluta till den angivna Event Hub.

SDK: er som du kan använda för att ansluta till den inbyggda Event Hub-kompatibla slutpunkten som IoT-hubb exponerar är:

| Språk | SDK | Exempel | Anteckningar |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Snabbstart](quickstart-send-telemetry-dotnet.md) | Använder Event Hubs-kompatibla information |
 Java | https://github.com/Azure/azure-event-hubs-java | [Snabbstart](quickstart-send-telemetry-java.md) | Använder Event Hubs-kompatibla information |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Snabbstart](quickstart-send-telemetry-node.md) | Använder IoT Hub-anslutningssträng |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Använder IoT Hub-anslutningssträng |

Produktintegreringar som du kan använda med den inbyggda Event Hub-kompatibla slutpunkten som IoT-hubb exponerar är:

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Se [bearbetning av data från IoT Hub med Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Se [Stream data som indata till Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/). Se [lägga till en IoT hub-händelsekälla i miljön för Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Du kan visa den [käll-kanalen](https://github.com/apache/storm/tree/master/external/storm-eventhubs) på GitHub.
* [Integration av Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Nästa steg

* Läs mer om IoT Hub-slutpunkter, [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md).

* Den [Snabbstarter](quickstart-send-telemetry-node.md) visar hur du skickar meddelanden från enheten till molnet från simulerade enheter och läsa meddelanden från den inbyggda slutpunkten. 

Mer information finns i den [Process IoT Hub enhet-till-moln-meddelanden med vägar](tutorial-routing.md) självstudien.

* Om du vill dirigera din enhet till moln-meddelanden till anpassade slutpunkter finns i [använder meddelandevägar och anpassade slutpunkter för meddelanden från enheten till molnet](iot-hub-devguide-messages-read-custom.md).
