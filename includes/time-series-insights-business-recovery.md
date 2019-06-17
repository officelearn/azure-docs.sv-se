---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431032"
---
## <a name="business-disaster-recovery"></a>Haveriberedskap för företag

Det här avsnittet beskriver funktionerna i Azure Time Series Insights som håller appar och tjänster som körs, även om en olycka inträffar (kallas även *företag haveriberedskap*).

### <a name="high-availability"></a>Hög tillgänglighet

Som en tjänst för Azure Time Series Insights tillhandahåller vissa *hög tillgänglighet* funktioner genom att använda uppsägningar på nivån Azure-region. Till exempel Azure stöder funktioner för katastrofåterställning via Azures *interregionala tillgänglighet* funktionen.

Ytterligare hög tillgänglighet funktioner från Azure (och även tillgänglig för alla Time Series Insights-instans):

- **Redundans**: Azure tillhandahåller [geo-replikering och belastningsutjämning](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Återställning av data** och **storage recovery**: Azure tillhandahåller [flera alternativ för att bevara och återställa data](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Webbplatsåterställning**: Azure tillhandahåller funktioner för site recovery via [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Kontrollera att du aktiverar relevanta Azure-funktionerna att tillhandahålla globala, över flera regioner för hög tillgänglighet för dina enheter och användare.

> [!NOTE]
> Om Azure är konfigurerad för att aktivera interregionala tillgänglighet, krävs ingen ytterligare interregionala tillgänglighetskonfiguration i Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT och event hubs

Vissa Azure IoT-tjänster är också inbyggda funktioner för katastrofåterställning:

- [Haveriberedskap för IoT Hub hög tillgänglighet](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), inklusive intra regional redundans
- [Händelsehubbprinciper](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Redundans i Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Integrera Time Series Insights med andra tjänster tillhandahåller ytterligare disaster recovery möjligheter. Telemetri som skickas till din event hub kan till exempel sparas till en säkerhetskopia Azure Blob storage-databasen.

### <a name="time-series-insights"></a>Time Series Insights

Det finns flera sätt att hålla din Time Series Insights-data, appar och tjänster som körs, även om de är störs. 

Du kan dock kontrollera att en fullständig säkerhetskopia av din Azure Time Series-miljö också krävs, för följande ändamål:

- Som en *redundans instans* specifikt för Time Series Insights att omdirigera data och trafik till
- Att bevara data och granskningsinformation

I allmänhet är det bästa sättet att duplicera en Time Series Insights-miljö att skapa en andra Time Series Insights-miljö i en säkerhetskopia av Azure-region. Händelser skickas också till den här sekundära miljön från din primära händelsekälla. Se till att du använder en andra, dedikerad konsumentgrupp. Följ den här källan företag disaster recovery riktlinjer enligt beskrivningen ovan.

Skapa en duplicerad miljö:

1. Skapa en miljö i en andra region. Mer information finns i [skapa en ny Time Series Insights-miljö i Azure-portalen](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Skapa en andra dedikerad konsumentgrupp för din händelsekälla.
1. Anslut den händelsekällan till den nya miljön. Se till att du anger den andra, dedikerad konsumentgruppen.
1. Granska Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) och [Händelsehubbar](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) dokumentation.

Om en händelse inträffar:

1. Om din primära region påverkas vid en katastrof incident, kan du dirigera om åtgärder för att säkerhetskopiera Time Series Insights-miljön.
1. Använd din andra region för att säkerhetskopiera och återställa alla Time Series Insights-telemetri och fråga data.

> [!IMPORTANT]
> Om det uppstår redundans:
> 
> * Även uppstå en fördröjning.
> * En tillfällig topp i meddelandehantering kan uppstå, eftersom åtgärder dirigeras.
> 
> Mer information finns i [Minimera svarstid i Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

