---
title: Vanliga frågor om priser för Azure Network Performance Monitor | Microsoft Docs
description: Vanliga frågor och svar – Azure Network Performance Monitor
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.component: ''
ms.openlocfilehash: 9e9fdfce9dbb165227f88e9f72bc219dce8f3307
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428692"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Prisändringar för Azure Network Performance Monitor

Vi har lyssnat på er feedback och nyligen introducerade en [ny prisnivå upplevelse](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) för olika övervakningstjänster i Azure. Den här artikeln fångar prisändringar som rör Azure [Övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) i ett enkelt att läsa frågor och svar-format.

Övervakare av nätverksprestanda består av tre komponenter:
* [Övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Övervakning av tjänstens slutpunkt](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor)
* [ExpressRoute-övervakning](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

I följande avsnitt beskrivs prisändringar för NPM-komponenter.

## <a name="performance-monitor"></a>Prestandaövervakning

**Hur var användningen av Prestandaövervakaren faktureras enligt den gamla modellen?**

Faktureringen för NPM baserades på användning och förbrukning av två komponenter:
* **Noder**: alla syntetiska transaktioner har sitt ursprung och avsluta på noderna. Noder är kallas även agenter eller Microsoft Hanteringsagenter.
* **Data**: resultatet av olika nätverkstester lagras i Azure Log Analytics-databasen.

Under den gamla modellen beräknades fakturan utifrån antalet noder och mängden data som genereras. 

**Hur debiteras användningen av Prestandaövervakaren under den nya modellen?**

Funktionen Prestandaövervakaren i NPM faktureras nu baserat på en kombination av: 

* Länkar till undernätverk övervakas
* Datavolym

**Vad är en länk för undernät?**

Övervakare av nätverksprestanda övervakar anslutning mellan två eller flera platser i nätverket. Anslutningen mellan en uppsättning noder eller agenter i ett undernät och en grupp av noder i ett annat undernät kallas en länk för undernätet.

**Jag har två undernät (A och B) och jag har flera agenter i varje undernät. Övervakare av nätverksprestanda övervakar anslutningen mellan alla agenter i Undernät A och alla agenter på undernätet B. Kommer jag att debiteras baserat på antalet anslutningar mellan undernät?**

Nej. I faktureringssyften grupperas alla anslutningar från undernät A till undernät B i en länk i undernätet. Du faktureras för en enda anslutning. Övervakare av nätverksprestanda fortsätter att övervaka anslutningen mellan olika agenter på varje undernät.

**Vad är kostnaderna för att övervaka en länk för undernät?**

Kostnaden för övervakning av en enda undernät länk under hela månaden, finns det [Ping nät](https://azure.microsoft.com/pricing/details/network-watcher/) avsnittet.

**Vad är kostnaderna för data som övervakaren av nätverksprestanda genererar?**

Kostar datainmatning (ladda upp data till Log Analytics, bearbetning och indexering) är tillgänglig på den [prissättningssidan](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics i avsnittet datainmatning. Avgift för datakvarhållning (d.v.s. data som kvarhålls enligt kundens val, utöver den första månaden) är även tillgängligt i den [prissättningssidan](https://azure.microsoft.com/pricing/details/log-analytics/), i avsnittet Datakvarhållning.


## <a name="expressroute-monitor"></a>ExpressRoute-övervakning

**Vad är avgifter för användningen av ExpressRoute-övervakning?**

Avgifter för ExpressRoute-övervakning faktureras baserat på mängden data som genereras under övervakning. Mer information finns i ”vad är kostnaderna för data som övervakaren av nätverksprestanda genererar”?

**Jag kan använda ExpressRoute-övervakning för att övervaka flera ExpressRoute-kretsar. Betalar jag baserat på antalet kretsar som övervakas?**

Du inte debiteras utifrån antingen antalet kretsar eller vilken typ av peering (till exempel privat peering, Microsoft-peering). Du debiteras baserat på mängden data, enligt beskrivningen ovan.

**Vad är mängden data som genereras när ExpressRoute övervakar en enda krets?**

Mängden data som genereras per månad, när övervakar ExpressRoute privat peering-anslutning, är följande:

|Percentil      |Data/månad (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Enligt den här tabellen Kunder den 50: e percentilen betalar för 192 MB data. $2.30 USD/GB för den första månaden, kostnaden för att övervaka en krets är USD $0.43 (= 192 * 2.30 / 1024).

**Vilka är några orsaker för mängden data?**

Mängden övervakningsdata som genereras beror på flera faktorer, till exempel:
* Antal agenter. Hur korrekt referensdatorns felisolering ökar med en ökning av antalet agenter.
* Antalet hopp i nätverket.
* Antalet sökvägar mellan källan och målet.

Till en högre percentiler (i tabellen ovan) vanligtvis övervaka sina kretsar från flera vantage punkter i sina lokala nätverk. Flera agenter placeras också djupare i nätverket, längre bort från service provider edge-routern. Agenterna placeras ofta på flera platser för användaren, filialer, rack i datacenter.

## <a name="service-endpoint-monitor"></a>Övervakning av tjänstens slutpunkt

**Vad är avgifter för användning av övervakning av tjänstens slutpunkt?**

Avgifter för användning av övervakning av tjänstens slutpunkt beräknas baserat på:
* Antal anslutningar
* Datavolym

**Vad är en anslutning?**

En anslutning är ett test av nätverksåtkomst till en slutpunkt (URL eller network service) från en enda agent under hela månaden. Till exempel kräver tre anslutningar att du övervakar en anslutning till bing.com från tre agenter.

**Vad är kostnaderna för övervakning av tjänstens slutpunkt?**

Referera till den [Anslutningsövervakning](https://azure.microsoft.com/pricing/details/network-watcher/) avsnittet för kostnaden av en slutpunkt för övervakning under hela månaden. Kostnad för data som är tillgänglig på den [prissättningssidan](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics i avsnittet datainmatning.

## <a name="references"></a>Referenser

[Logga Analytics priser vanliga frågor och svar](https://azure.microsoft.com/pricing/details/log-analytics/): The vanliga frågor och svar har information på nivån kostnadsfri, per nod och andra prisinformation.

