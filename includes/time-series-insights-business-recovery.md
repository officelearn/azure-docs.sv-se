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
ms.openlocfilehash: cf07d19966c08a63b9aa50475622aa0a1e5e1600
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236510"
---
## <a name="business-disaster-recovery"></a>Haveriberedskap för företag

Det här avsnittet beskriver funktionerna i Azure Time Series Insights som håller appar och tjänster som körs även om en olycka inträffar (**företag haveriberedskap**).

### <a name="high-availability"></a>Hög tillgänglighet

Som en tjänst för Azure Time Series Insights tillhandahåller vissa **hög tillgänglighet** funktioner med hjälp av uppsägningar på nivån Azure-region. Till exempel Microsoft Azure stöder funktioner för katastrofåterställning via Azures **interregionala tillgänglighet** funktionen.

Hög tillgänglighet för ytterligare funktioner via Azure (och även tillgänglig för alla Time Series Insights-instans):

1. **Redundans**: Azure tillhandahåller [geo-replikering och läsa in belastningsutjämning](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
1. **Återställning av data** och **storage recovery**: Azure tillhandahåller [flera alternativ för att bevara och återställa data](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
1. **Site Recovery** funktioner via [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Se till att aktivera dessa funktioner som Azure för att tillhandahålla global, över flera regioner, hög tillgänglighet för dina enheter och användare.

> [!NOTE]
> Om Azure har konfigurerats för att aktivera **interregionala tillgänglighet**, krävs ingen ytterligare interregionala tillgänglighetskonfiguration i Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT och Event hubs

Vissa Azure IoT-tjänster är också inbyggda funktioner för katastrofåterställning:

1. [Haveriberedskap för IoT Hub hög tillgänglighet](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr) inklusive intra regional redundans.
1. [Event Hub-principer](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).
1. [Redundans i Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

Integrera Time Series Insights med följande tjänster tillhandahåller ytterligare disaster recovery möjligheter. Telemetri som skickas till din Event Hub kan exempelvis vara beständig till en säkerhetskopia Azure Blob Storage-databasen.

### <a name="time-series-insights"></a>Time Series Insights

Det finns flera sätt att hålla din Time Series Insights-data, appar och tjänster som körs även om de är störs. Du kan även bestämma att det krävs en fullständig, dubbla, säkerhetskopiering kopia av din Azure Time Series-miljö.

1. Som en TSI-specifika **redundans instans** att omdirigera data och trafik till.
1. För granskning och data bevaras.

I allmänhet är det bästa sättet att duplicera en TSI-miljö att skapa en andra TSI-miljö i en säkerhetskopia av Azure-region. Händelser skickas också till den här sekundära miljön från din primära händelsekälla. Se till att använda en andra, dedikerad konsumentgrupp och följer den här källan företag disaster recovery (ovan).

Mer specifikt att skapa en duplicerad miljö:

1. Skapa en miljö i en andra region ([skapa en ny Time Series Insights-miljö i Azure-portalen](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)).
1. Skapa en andra dedikerad konsumentgrupp för din händelsekälla.
1. Anslut den händelsekällan till den nya miljön att se till att ange den andra, dedikerad konsumentgruppen.
1. Granska Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) och [händelsehubb](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentation.

Slutligen:

* Om din primära region påverkas vid en katastrof incident, kan du dirigera om åtgärder för att säkerhetskopiera Time Series Insights-miljön.
* Använd din andra region för att säkerhetskopiera och Återställ alla TSI telemetri och fråga data.

> [!IMPORTANT]
> * Observera att du kan uppstått en fördröjning i händelse av redundans.
> * Redundans kan också orsaka en tillfällig topp under bearbetningen av meddelandet som åtgärder dirigeras.
> * Mer information finns i [Minimera svarstid i Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).