---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013659"
---
## <a name="business-disaster-recovery"></a>Återhämtning av affärskatastrofer

I det här avsnittet beskrivs funktioner i Azure Time Series Insights som håller appar och tjänster igång, även om en katastrof inträffar (så kallad *återställning av affärskatastrofer).*

### <a name="high-availability"></a>Hög tillgänglighet

Som en Azure-tjänst tillhandahåller Time Series Insights vissa *funktioner med hög tillgänglighet* med hjälp av redundanser på Azure-regionnivå. Azure stöder till exempel funktioner för haveriberedskap via Azures *tillgänglighetsfunktion mellan regioner.*

Ytterligare funktioner med hög tillgänglighet som tillhandahålls via Azure (och även tillgängliga för alla Time Series Insights-instanser) inkluderar:

- **Redundans:** Azure tillhandahåller [geo-replikering och belastningsutjämning](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Återställning och** **lagringsåterställning av data**: Azure innehåller flera alternativ för att bevara och återställa [data](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Azure Site Recovery:** Azure tillhandahåller funktioner för webbplatsåterställning via [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
- **Azure Backup**: [Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) stöder både lokal säkerhetskopiering och säkerhetskopiering i molnet av virtuella Azure-datorer.

Se till att du aktiverar relevanta Azure-funktioner för att ge global, regionöverskridande hög tillgänglighet för dina enheter och användare.

> [!NOTE]
> Om Azure är konfigurerat för att aktivera tillgänglighet mellan regioner krävs ingen ytterligare konfiguration för tillgänglighet mellan regioner i Azure Time Series Insights.

### <a name="iot-and-event-hubs"></a>IoT- och händelsehubbar

Vissa Azure IoT-tjänster innehåller även inbyggda funktioner för återställning av affärskatastrofer:

- [Azure IoT Hub hög tillgänglighet katastrofåterställning](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), som inkluderar intra-region redundans
- [Principer för Azure-händelsehubbar](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Redundans för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Genom att integrera Time Series Insights med de andra tjänsterna kan du ytterligare möjligheter till katastrofåterställning. Telemetri som skickas till din händelsenav kan till exempel sparas i en databas för Azure Blob-lagring säkerhetskopia.

### <a name="time-series-insights"></a>Time Series Insights

Det finns flera sätt att hålla dina Time Series Insights-data, appar och tjänster igång, även om de är störda. 

Du kan dock fastställa att en fullständig säkerhetskopia av din Azure Time Series-miljö också krävs, för följande ändamål:

- Som en *redundansinstans* specifikt för Time Series Insights för att omdirigera data och trafik till
- Så här bevarar du data- och granskningsinformation

I allmänhet är det bästa sättet att duplicera en Time Series Insights-miljö att skapa en andra Time Series Insights-miljö i en Azure-region för säkerhetskopiering. Händelser skickas också till den här sekundära miljön från din primära händelsekälla. Se till att du använder en andra dedikerad konsumentgrupp. Följ den källans riktlinjer för kostnadsåterställning av företag, som beskrivits tidigare.

Så här skapar du en dubblettmiljö:

1. Skapa en miljö i en andra region. Mer information finns [i Skapa en ny Time Series Insights-miljö i Azure-portalen](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Skapa en andra dedikerad konsumentgrupp för din händelsekälla.
1. Anslut händelsekällan till den nya miljön. Se till att du anger den andra dedikerade konsumentgruppen.
1. Granska dokumentationen för Tidsseriestatistik [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) och [Event Hubs.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)

Om en händelse inträffar:

1. Om din primära region påverkas under en katastrofincident omdirigerar du åtgärder till time series insights-miljön för säkerhetskopiering.
1. Använd din andra region för att säkerhetskopiera och återställa alla Time Series Insights telemetri och frågedata.

> [!IMPORTANT]
> Om en redundans inträffar:
> 
> * En fördröjning kan också uppstå.
> * En tillfällig ökning av meddelandebearbetningen kan inträffa när åtgärder omdirigeras.
> 
> Mer information finns [i Minska svarstiden i Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

