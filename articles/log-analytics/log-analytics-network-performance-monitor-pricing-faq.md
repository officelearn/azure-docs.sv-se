---
title: Vanliga frågor och svar priser för Azure-nätverk Prestandaövervakaren | Microsoft Docs
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
ms.component: na
ms.openlocfilehash: 96eb26d6a4faf8c6907d23ebf21f2446722c913b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127101"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Priser ändringar för Azure Network Performance Monitor

Vi har lyssnat på din feedback och nyligen lades en [nya priser upplevelse](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) för olika övervaka tjänster i Azure. Den här artikeln innehåller de prisnivå ändringar som är relaterade till Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) i ett enkelt att läsa fråga och svar format.

Network Performance Monitor består av tre komponenter:
* [Prestandaövervakaren](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Tjänsten Endpoint för övervakning](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor)
* [Övervakare för ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

I följande avsnitt beskrivs prisnivå ändringarna för NPM-komponenter.

## <a name="performance-monitor"></a>Prestandaövervakning

**Hur var användning av Prestandaövervakaren debiteras i gamla modellen?**

Faktureringen för NPM var baserat på användning och förbrukning av två komponenter:
* **Noder**: alla syntetiska transaktioner som har sitt ursprung och avsluta på noderna. Noder är kallas även för agenter eller Microsoft Hanteringsagenter.
* **Data**: resultatet av olika nätverkstester lagras i Azure Log Analytics-databasen.

Med den gamla modellen har växeln beräknas baserat på antalet noder och mängden data som genereras. 

**Hur debiteras användning av Prestandaövervakaren under den nya modellen?**

Funktionen Prestandaövervakaren i NPM nu faktureras baserat på en kombination av: 

* Undernät länkar som övervakas
* Datavolym

**Vad är en länk för undernät?**

Prestandaövervakaren övervakar anslutning mellan två eller flera platser i nätverket. Anslutningen mellan en uppsättning noder eller agenter i ett undernät och en grupp av noder i ett annat undernät kallas för en länk i undernätet.

**Jag har två undernät (A och B) och flera agenter har i varje undernät. Prestandaövervakaren övervakar anslutningen från alla agenter i Undernät A till alla agenter på undernätet B. Jag debiteras baserat på antalet anslutningar mellan undernät?**

Nej. Alla anslutningar från undernät A till undernät B grupperas tillsammans för fakturering, till en länk i undernätet. Det är du debiteras för en enda anslutning. Prestandaövervakaren fortsätter att övervaka anslutningen mellan olika agenter på varje undernät.

**Vad är kostnader för att övervaka en länk för undernät?**

Kostnaden för att övervaka en enda undernät länk för hela månaden, finns det [Ping nät](https://azure.microsoft.com/pricing/details/network-watcher/) avsnitt.

**Vad är avgifterna för data som genereras av Prestandaövervakaren?**

Kostnad för införandet (dataöverföringen till logganalys bearbetning och indexering) är tillgängligt på den [sida med priser](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics i avsnittet Datapåfyllning. Kostnad för datalagring (det vill säga data som behålls enligt kundens val efter den första månaden) är också tillgängligt på den [sida med priser](https://azure.microsoft.com/pricing/details/log-analytics/), i avsnittet datalagring.


## <a name="expressroute-monitor"></a>ExpressRoute-övervakning

**Vad är avgifter för användning av ExpressRoute-Övervakaren?**

Kostnader för ExpressRoute-övervakaren debiteras baserat på mängden data som genereras under övervakning. Mer information finns i ”vad är avgifterna för data som genereras av Prestandaövervakaren”?

**Jag kan använda ExpressRoute Övervakaren för att övervaka flera ExpressRoute-kretsar. Kan jag debiteras baserat på antalet kretsar övervakas?**

Du inte debiteras baserat på antingen antalet kretsar eller typen av peering (till exempel privat peering, Microsoft-peering). Du debiteras baserat på mängden data som tidigare förklarats.

**Vad är mängden data som genereras när ExpressRoute övervakar en enda krets?**

Mängden data som genereras per månad, när ExpressRoute övervakar en privat peering-anslutning, är följande:

|Percentil      |Data per månad (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Kunder på den 50: e percentilen betalar för 192 MB data enligt den här tabellen. $2.30 USD/GB för den första månaden, är kostnaden för att övervaka en krets USD $0.43 (= 192 * 2.30 / 1024).

**Vad är olika orsaker för variationer i mängden data?**

Mängden övervakningsdata genereras beror på flera faktorer, till exempel:
* Antalet agenter. Riktighet fel isolering ökar med en ökning av antalet agenter.
* Antalet hopp i nätverket.
* Antal sökvägar mellan källan och målet.

Kunder med högre percentiler (i tabellen ovan) vanligtvis övervaka deras kretsar från flera vantage punkter på sina lokala nätverk. Flera agenter placeras också djupare i nätverket, längre bort från service provider gränsroutern. Agenterna placeras ofta på flera platser för användaren, filialer, rack i datacenter.

## <a name="service-endpoint-monitor"></a>Tjänsten Endpoint för övervakning

**Vad är avgifter för användning av tjänsten Endpoint övervakning?**

Avgifter för användning av tjänsten Endpoint övervakning beräknas baserat på:
* Antalet anslutningar
* Mängden data

**Vad är en anslutning?**

En anslutning är ett test av tillgänglighet till en slutpunkt (URL eller network service) från en enskild agent för hela månaden. Till exempel utgör övervakning en anslutning till bing.com från tre agenter tre anslutningar.

**Vad är kostnader för tjänsten Endpoint övervakning?**

Referera till den [anslutning övervakning](https://azure.microsoft.com/pricing/details/network-watcher/) avsnittet för kostnaden för att övervaka en slutpunkt för hela månaden. Kostnad för data som är tillgängligt på den [sida med priser](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics i avsnittet Datapåfyllning.

## <a name="references"></a>Referenser

[Logga Analytics priser vanliga frågor och svar](https://azure.microsoft.com/pricing/details/log-analytics/): det vanliga frågor och svar avsnittet innehåller information om kostnadsfria nivån per nod priser och andra prisinformation.

