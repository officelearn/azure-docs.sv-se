---
title: Vanliga frågor och svar om priser för Azure Network Performance Monitor | Microsoft-dokument
description: Vanliga frågor och svar - Azure Network Performance Monitor
ms.subservice: logs
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: c5a80c7ff6d08ce601a6c2bbb0db0ef866d3b425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654400"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Prisändringar för Azure Network Performance Monitor

Vi har lyssnat på din feedback och nyligen infört en [ny prisupplevelse](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) för olika övervakningstjänster i Azure. Den här artikeln fångar prisändringarna relaterade till Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) i ett lättläst fråge- och svarsformat.

Network Performance Monitor består av tre komponenter:
* [Prestandaövervakaren](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Övervakare av tjänstslutpunkt](https://docs.microsoft.com/azure/networking/network-monitoring-overview)
* [ExpressRoute-övervakare](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

I följande avsnitt beskrivs prisändringarna för NPM-komponenterna.

## <a name="performance-monitor"></a>Prestandaövervakaren

**Hur fakturerades användning av prestandaövervakaren i den gamla modellen?**

Faktureringen för NPM baserades på användning och förbrukning av två komponenter:
* **Noder**: Alla syntetiska transaktioner har sitt ursprung och slutar vid noderna. Noder kallas också agenter eller Microsoft Management Agents.
* **Data**: Resultaten av de olika nätverkstesterna lagras på log analytics-arbetsytan.

Under den gamla modellen beräknades fakturan baserat på antalet noder och mängden data som genererades. 

**Hur debiteras användning av Prestandaövervakaren enligt den nya modellen?**

Prestandaövervakarfunktionen i NPM faktureras nu baserat på en kombination av: 

* Nätlänkar övervakade
* Datavolym

**Vad är en nätlänk?**

Performance Monitor övervakar anslutningen mellan två eller flera platser i nätverket. Kopplingen mellan en grupp noder eller agenter i ett undernät och en grupp noder i ett annat undernät kallas för en nätlänk.

**Jag har två undernät (A och B), och jag har flera agenter på varje undernät. Performance Monitor övervakar anslutningen från alla agenter i undernät A till alla agenter på undernät B. Kommer jag att debiteras baserat på antalet anslutningar mellan undernät?**

Nej. För faktureringsändamål grupperas alla anslutningar från undernät A till undernät B i en undernätslänk. Du faktureras för en enda anslutning. Performance Monitor fortsätter att övervaka anslutningen mellan olika agenter i varje undernät.

**Vilka är kostnaderna för att övervaka en nätlänk?**

Kostnader för övervakning av en enda undernätslänk för hela månaden finns i avsnittet [Ping Mesh.](https://azure.microsoft.com/pricing/details/network-watcher/)

**Vilka avgifter debiteras för data som Performance Monitor genererar?**

Avgiften för inmatning (dataöverföring till Log Analytics-arbetsyta i Azure Monitor, bearbetning och indexering) är tillgänglig på [prissidan](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics i avsnittet Datainmatning. Avgiften för datalagring (det vill än data som lagras efter kundens val, utöver den första månaden) är också tillgänglig på [prissidan](https://azure.microsoft.com/pricing/details/log-analytics/)i avsnittet Datalagring.


## <a name="expressroute-monitor"></a>ExpressRoute-övervakare

**Vilka avgifter debiteras för användning av ExpressRoute Monitor?**

Avgifter för ExpressRoute Monitor faktureras baserat på mängden data som genereras under övervakningen. Mer information finns i "Vilka är avgifterna för data som Prestandaövervakaren genererar?"

**Jag använder ExpressRoute Monitor för att övervaka flera ExpressRoute-kretsar. Debiteras jag baserat på antalet kretsar som övervakas?**

Du debiteras inte baserat på antingen antalet kretsar eller typen av peering (till exempel privat peering, Microsoft peering). Du debiteras baserat på mängden data, som tidigare förklarats.

**Hur stor är mängden data som genereras när ExpressRoute övervakar en enda krets?**

Mängden data som genereras per månad, när ExpressRoute övervakar en privat peering-anslutning, är följande:

|Percentil      |Data/månad (MB)|
| :---:          |           ---:|
|<sup>50.</sup> |            192|
|<sup>60.</sup> |            256|
|<sup>70.</sup> |            360|
|<sup>80.</sup> |            498|
|<sup>90.</sup> |            870|
|<sup>95.</sup> |           1560|


Enligt denna tabell, kunder på den 50: e percentilen betala för 192 MB data. På USD $2.30/GB för den första månaden, kostar uppstått för att övervaka en krets är USD $0.43 (= 192 * 2.30 / 1024).

**Vilka är några orsaker till variationer i datavolymen?**

Mängden övervakningsdata som genereras beror på flera faktorer, till exempel:
* Antal agenter. Noggrannheten i felisolering ökar med en ökning av antalet agenter.
* Antal hopp i nätverket.
* Antal sökvägar mellan källan och målet.

Kunder vid de högre percentilerna (i föregående tabell) övervakar vanligtvis sina kretsar från flera utsiktspunkter i sitt lokala nätverk. Flera agenter placeras också djupare i nätverket, längre från tjänsteleverantörens kantrouter. Agenterna placeras ofta på flera användarwebbplatser, grenar och rack i datacenter.

## <a name="service-endpoint-monitor"></a>Övervakare av tjänstslutpunkt

**Vilka avgifter debiteras för användning av Service Endpoint Monitor?**

Avgifter för användning av Service Endpoint Monitor beräknas baserat på:
* Antal anslutningar
* Datavolym

**Vad är en koppling?**

En anslutning är ett test av nåbarhet till en slutpunkt (URL eller nätverkstjänst) från en enda agent för hela månaden. Att övervaka en anslutning till bing.com från tre agenter utgör till exempel tre anslutningar.

**Vilka är kostnaderna för Service Endpoint Monitor?**

Se avsnittet [Anslutningsövervakning](https://azure.microsoft.com/pricing/details/network-watcher/) för kostnaden för att övervaka en slutpunkt för hela månaden. Avgiften för data är tillgänglig på [prissidan](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics i avsnittet Datainmatning.

## <a name="references"></a>Referenser

[Vanliga frågor och svar om logganalyspriser](https://azure.microsoft.com/pricing/details/log-analytics/): Avsnittet Vanliga frågor och svar innehåller information om kostnadsfri nivå, per nodprissättning och annan prisinformation.

