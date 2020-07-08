---
title: Data flödes begränsningar för strömmande inmatning – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om inkommande data flödes gränser i Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: d5a82aa3aca55622308cae5816c95df9766048da
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050072"
---
# <a name="streaming-ingestion-throughput-limits"></a>Data flödes gränser för strömning

Begränsningar för Azure Time Series Insights strömmande data ingress beskrivs nedan.

> [!TIP]
> Läs [Planera din för hands versions miljö](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) för en omfattande lista över alla begränsningar för för hands versionen. Att **ta bort** ordet "Preview" här när artikel rubriken har uppdaterats.

## <a name="per-environment-limitations"></a>Begränsningar per miljö

I allmänhet visas ingångs priser som faktor för antalet enheter i din organisation, frekvens för händelse utsläpp och storleken på varje händelse:

*  **Antal enheter** × **händelse utsläpps frekvens** × **storlek på varje händelse**.

Som standard kan Time Series Insights mata in inkommande data med en hastighet på **upp till 1 megabyte per sekund (Mbit/s) per Time Series Insights-miljö**. Det finns ytterligare begränsningar [per nav-partition](concepts-streaming-throughput-limitations.md#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Miljö stöd för att mata in hastigheter på upp till 16 MBps kan tillhandahållas av begäran.
> * Kontakta oss om du behöver mer data flöde genom att skicka in ett support ärende via Azure Portal.
 
* **Exempel 1:**

    Contoso-leverans har 100 000 enheter som genererar en händelse tre gånger per minut. Storleken på en händelse är 200 byte. De använder en IoT Hub med fyra partitioner som Time Series Insights händelse källa.

    * Inmatnings takten för Time Series Insightss miljön blir: **100 000 enheter * 200 byte/event * (3/60 Event/s) = 1 Mbit/s**.
    * Inmatnings hastigheten per partition blir 0,25 Mbit/s.
    * Contosos överförings takt skulle ligga inom skalnings begränsningen.

* **Exempel 2:**

    Contoso flotta Analytics har 60 000 enheter som genererar en händelse varje sekund. De använder en Event Hub med ett antal partitioner på 4 som den Time Series Insights händelse källan. Storleken på en händelse är 200 byte.

    * Miljöns utmatnings frekvens skulle vara: **60 000 enheter * 200 byte/event * 1 händelse/SEK = 12 Mbit/s**.
    * Antalet per partition skulle bli 3 Mbit/s.
    * Contoso flottans analys "inmatnings takt är över miljön och partitionens gränser. De kan skicka en begäran till Time Series Insights via Azure Portal för att öka inmatnings takten för deras miljö och skapa en Händelsehubben med fler partitioner för att ligga inom gränserna för för hands versionen.

## <a name="hub-partitions-and-per-partition-limits"></a>NAV partitioner och gränser per partition

När du planerar Time Series Insightss miljön är det viktigt att du funderar på konfigurationen av de händelse källor som du kommer att ansluta till Time Series Insights. Både Azure IoT Hub och Event Hubs använder partitioner för att aktivera horisontell skalning för händelse bearbetning. 

En *partition* är en ordnad sekvens med händelser som lagras i en hubb. Antalet partitioner anges under fasen skapande av hubb och kan inte ändras.

För Event Hubs partitionering bör du läsa mer om [hur många partitioner behöver jag?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> De flesta IoT-hubbar som används med Azure Time Series Insights behöver bara fyra partitioner.

Oavsett om du skapar en ny hubb för din Time Series Insights-miljö eller använder ett befintligt, måste du beräkna din användnings takt per partition för att avgöra om den ligger inom gränserna. 

Azure Time Series Insights för hands versionen har för närvarande en **gräns för per partition på 0,5 Mbit/s**.

### <a name="iot-hub-specific-considerations"></a>IoT Hub-/regionsspecifika överväganden

När en enhet skapas i IoT Hub tilldelas den permanent till en partition. När du gör det kan IoT Hub garantera händelse ordning (eftersom tilldelningen aldrig ändras).

En fast tilldelning av partitioner påverkar också Time Series Insights instanser som matar in data som skickas från IoT Hub. När meddelanden från flera enheter vidarebefordras till hubben med samma Gateway-enhets-ID kan de komma i samma partition samtidigt som de kan överskrida gränserna för varje partition.

**Påverkan**:

* Om en enda partition får en varaktig takt med att mata in över för hands versionen, är det möjligt att Time Series Insights inte synkroniserar all telemetri innan IoT Hub data lagrings perioden har överskridits. Därför kan skickade data gå förlorade om inmatnings gränserna överskrids konsekvent.

För att minimera denna omständighet rekommenderar vi följande metod tips:

* Beräkna dina priser per miljö och per partition innan du distribuerar din lösning.
* Se till att dina IoT Hub enheter är belastningsutjämnade i det fall där det är möjligt.

> [!IMPORTANT]
> För miljöer som använder IoT Hub som en händelse källa beräknar du inmatnings takten med antalet NAV enheter som används för att se till att priset sjunker under 0,5 Mbit/s per begränsning i partitionen.
>
> * Även om flera händelser anländer samtidigt, överskrids inte för hands versions gränsen.

  ![IoT Hub partitions diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Se följande resurser för att lära dig mer om hur du optimerar hubben och partitionerna:

* [IoT Hub skala](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event Hub-skala](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event Hub-partitioner](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>Nästa steg

* Läs om data [lagring](concepts-storage.md)