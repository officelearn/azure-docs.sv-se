---
title: ta med fil
description: ta med fil
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: e3e7044148e262e6977ae3be96f7cb61218114a3
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388749"
---
## <a name="business-disaster-recovery"></a>Haveriberedskap för företag

Det här avsnittet beskriver funktionerna i Azure Time Series Insights som håller appar och tjänster som körs även om en katastrof inträffar. Detta kallas för katastrofåterställning för företag.

### <a name="high-availability"></a>Hög tillgänglighet

Time Series Insights tillhandahåller vissa funktioner för hög tillgänglighet med hjälp av uppsägningar på nivån Azure-region som en Azure-tjänst. Till exempel stöder Microsoft Azure haveriberedskap med via funktionen för Azures interregionala tillgänglighet.

Ytterligare funktioner för hög tillgänglighet via Azure, och som också är tillgängliga för en Time Series Insights-instans, inkluderar:

* **Redundans**: Azure tillhandahåller [geo-replikering och belastningsutjämning](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
* **Återställning av data** och **storage recovery**: Azure tillhandahåller [flera alternativ för att bevara och återställa data](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
* **Webbplatsåterställning**: Funktioner som är tillgängliga via [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Se till att aktivera dessa funktioner som Azure för att tillhandahålla hög tillgänglighet för globala, över flera regioner för dina enheter och användare.

> [!NOTE]
> Om Azure är konfigurerad för att aktivera interregionala tillgänglighet, krävs ingen ytterligare interregionala tillgänglighetskonfiguration i Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT och event hubs

Vissa Azure IoT-tjänster är också inbyggda funktioner för katastrofåterställning:

* [Azure IoT Hub hög tillgänglighet-katastrofåterställning](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), vilket inkluderar intra regional redundans.
* [Azure Event Hubs-principer](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
* [Redundans i Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

Integrera Time Series Insights med följande tjänster tillhandahåller ytterligare disaster recovery möjligheter. Telemetri som skickas till din event hub kan exempelvis vara beständig till en säkerhetskopia Azure Blob storage-databasen.

### <a name="time-series-insights"></a>Time Series Insights

Det finns flera sätt att hålla din Time Series Insights-data, appar och tjänster som körs även om de är störs. Du kan även bestämma att det krävs en fullständig säkerhetskopia av din Azure Time Series-miljö.

* Som en Time Series Insights-specifika redundans instans att omdirigera data och trafik till.
* För granskning och data bevaras.

I allmänhet är det bästa sättet att duplicera en Time Series Insights-miljö att skapa en andra Time Series Insights-miljö i en säkerhetskopia av Azure-region. Händelser skickas också till den här sekundära miljön från din primära händelsekälla. Se till att använda en andra dedikerad konsumentgrupp och följ den här källan företag disaster recovery riktlinjerna, som tidigare.

Mer specifikt att skapa en duplicerad miljö:

1. Skapa en miljö i en andra region. Anvisningar finns i [skapa en ny Time Series Insights-miljö i Azure-portalen](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Skapa en andra dedikerad konsumentgrupp för din händelsekälla.
1. Anslut den händelsekällan till den nya miljön. Se till att ange andra dedikerad konsumentgrupp.
1. Granska Time Series Insights [IoT-hubb](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) och [händelsehubb](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentation.

Slutligen:

1. Om din primära region påverkas vid en katastrof incident, kan du dirigera om åtgärder för att säkerhetskopiera Time Series Insights-miljön.
1. Använd din andra region för att säkerhetskopiera och återställa alla Time Series Insights-telemetri och fråga data.

> [!IMPORTANT]
> * Observera att du kan uppstått en fördröjning i händelse av redundans.
> * Redundans kan också orsaka en tillfällig topp under bearbetningen av meddelandet som åtgärder dirigeras.
> * Mer information finns i [Minimera svarstid i Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).
