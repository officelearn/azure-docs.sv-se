---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77013659"
---
## <a name="business-disaster-recovery"></a>Katastrof återställning för företag

I det här avsnittet beskrivs funktioner i Azure Time Series Insights som håller appar och tjänster som körs, även om en katastrof inträffar (kallas även för *verksamhets haveri beredskap*).

### <a name="high-availability"></a>Hög tillgänglighet

Som Azure-tjänst tillhandahåller Time Series Insights vissa funktioner för *hög tillgänglighet* genom att använda redundans på region nivån i Azure. Azure stöder exempelvis haveri beredskap via Azures *tillgänglighets funktion över flera regioner* .

Ytterligare funktioner med hög tillgänglighet tillhandahålls via Azure (och även tillgängligt för valfri Time Series Insights instans):

- **Redundans**: Azure tillhandahåller [geo-replikering och belastnings utjämning](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Data återställning** och **lagrings återställning**: Azure tillhandahåller [flera alternativ för att bevara och återställa data](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Azure Site Recovery**: Azure tillhandahåller Site Recovery-funktioner via [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
- **Azure Backup**: [Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) stöder både lokal och molnad säkerhets kopiering av virtuella Azure-datorer.

Se till att du aktiverar de relevanta Azure-funktionerna för att tillhandahålla global tillgänglighet över flera regioner för dina enheter och användare.

> [!NOTE]
> Om Azure är konfigurerat för att aktivera tillgänglighet mellan regioner krävs ingen ytterligare tillgänglighets konfiguration mellan regioner i Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT-och event-hubbar

Vissa Azure IoT-tjänster omfattar även inbyggda funktioner för verksamhets haveri beredskap:

- [Azure IoT Hub haveri beredskap med hög tillgänglighet](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), vilket omfattar redundans inom regioner
- [Azure Event Hubs-principer](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure Storage redundans](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Att integrera Time Series Insights med de andra tjänsterna ger ytterligare möjligheter till haveri beredskap. Till exempel kan telemetri som skickas till händelsehubben vara bestående av en säkerhets kopia av Azure Blob Storage-databasen.

### <a name="time-series-insights"></a>Time Series Insights

Det finns flera sätt att se till att dina Time Series Insights data, appar och tjänster körs, även om de är störda. 

Du kan dock bestämma att en fullständig säkerhets kopia av din Azure Time Series-miljö också krävs, i följande syfte:

- Som en *redundansväxling* särskilt för Time Series Insights att omdirigera data och trafik till
- Bevara data och gransknings information

I allmänhet är det bästa sättet att duplicera en Time Series Insights miljö att skapa en andra Time Series Insights-miljö i en Azure Backup-region. Händelser skickas också till den sekundära miljön från den primära händelse källan. Se till att du använder en andra dedikerad konsument grupp. Följ dessa rikt linjer för affärs haveri beredskap för källan, enligt beskrivningen ovan.

Så här skapar du en duplicerad miljö:

1. Skapa en miljö i en andra region. Mer information finns [i skapa en ny Time Series Insights-miljö i Azure Portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Skapa en andra dedikerad konsument grupp för din händelse källa.
1. Anslut händelse källan till den nya miljön. Se till att du anger den andra dedikerade konsument gruppen.
1. Läs dokumentationen om Time Series Insights [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) och [Event Hubs](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) .

Om en händelse inträffar:

1. Om din primära region påverkas under en katastrof incident, dirigerar du om åtgärder till säkerhets kopierings Time Series Insightss miljön.
1. Använd din andra region för att säkerhetskopiera och återställa alla Time Series Insights telemetri och fråga efter data.

> [!IMPORTANT]
> Om en redundansväxling inträffar:
> 
> * En fördröjning kan också uppstå.
> * En tillfällig insamling i meddelande bearbetningen kan inträffa, eftersom åtgärder dirigeras om.
> 
> Mer information finns i avsnittet [minska svars tiden i Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

