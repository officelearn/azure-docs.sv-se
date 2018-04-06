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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 84a3834416c7d93ecb4f51fea9c7baed8af85db0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Priser ändringar för Azure-nätverk Prestandaövervakaren

Vi har lyssnat på du och nyligen har introducerats en [nya priser upplevelse](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), för olika övervaka tjänster i Azure.

Det här dokumentet innehåller de prisnivå ändringar som är relaterade till Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) i något lättläst format för frågor och svar.

Network Performance Monitor består av tre komponenter:
* [Prestandaövervakaren](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Tjänsten Endpoint övervakaren](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor) och
* [Övervakare för ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

Avsnittet nedan förklaras prisnivå ändringar för Prestandaövervakaren, ExpressRoute-övervakaren och Endpoint-tjänsten för övervakning.

## <a name="performance-monitor-pm"></a>Prestandaövervakaren (PM)

**Hur var användning av Prestandaövervakaren debiteras i gamla schemat?**

Faktureringen för NPM baserades på användningsförbrukning av två komponenter:
* Noder: Alla syntetiska transaktioner som har sitt ursprung och avsluta på noderna. Noder är kallas även för agenter eller MMA (Microsoft Management agenter).
* Data: Resultatet av olika nätverkstester lagras i logganalys-databasen.

Med den gamla modellen har växeln beräknas baserat på antalet noder och mängden data som genereras. 

**Hur är användning av Prestandaövervakaren debiteras under den nya modellen?**

Funktionen Prestandaövervakaren i NPM faktureras nu på en kombination av: 

* undernät länkar som övervakas och
* Datavolym

**Vad är en länk för undernät?**

Prestandaövervakaren övervakar anslutning mellan två eller flera platser i nätverket.  Anslutningen mellan en uppsättning noder/agenter i ett undernät och en grupp av noder i ett annat undernät kallas för en länk i undernätet.

**Jag har två undernät (Undernät A och B) och har flera agenter i varje undernät.  Prestandaövervakaren övervakar anslutningen från alla agenter i Undernät A till alla agenter på undernätet B.  Jag debiteras baserat på antalet anslutningar mellan undernät?**

Nej. Alla anslutningar från undernät A till B-undernät grupperas tillsammans i ett undernät länk för fakturering, och du debiteras för en enda anslutning.  Prestandaövervakaren kommer att fortsätta att övervaka anslutningen mellan olika agenter på varje undernät.

**Vad är kostnader för att övervaka en länk för undernät?**

Referera till avsnittet [Ping nät](https://azure.microsoft.com/pricing/details/network-watcher/) för kostnaden för att övervaka en enda undernät länk för hela månaden.

**Vad är avgifterna för data som genereras av Prestandaövervakaren?**

Kostnad för införandet (dataöverföringen till logganalys bearbetning och indexering) är tillgängligt på den [sida med priser](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.  (Avsnittet: Datapåfyllning).

Kostnad för datalagring (det vill säga data som behålls enligt kundens val efter den första månaden) är också tillgängligt på den [sida med priser](https://azure.microsoft.com/pricing/details/log-analytics/).  (Avsnittet: datalagring).


## <a name="expressroute-monitor-erm"></a>ExpressRoute-övervakaren (ERM)

**Vad är avgifter för användning av ExpressRoute-Övervakaren?**

Kostnader för ExpressRoute-övervakaren debiteras baserat på mängden data som genereras under övervakning.   Referera till frågan ”vad är avgifterna för data som genereras av Prestandaövervakaren”? Mer information.

**Jag använder ERM för att övervaka flera ExpressRoute-kretsar. Kan jag debiteras baserat på antalet kretsar övervakas?**

Du inte debiteras baserat på antingen antalet kretsar eller typen av peering (till exempel privat peering, Microsoft-peering).  Du debiteras på mängden data, enligt beskrivningen ovan.

**Vad är mängden data som genereras vid övervakning av en enda krets?**

Den genererade datavolymen per månad, övervakning av en privat peering-anslutning när är följande:

|Percentil      |Data per månad (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Kunder på den 50: e percentilen betalar för 192 MB data per tabellen ovan. $2.30 USD/GB för den första månaden, är kostnaden för att övervaka en krets USD 0.43 (= 192 * 2.30 / 1024) för den första månaden.

**Vad är ett antal anledningar för variationer i mängden data?**

Mängden övervakningsdata genereras beror på flera faktorer, till exempel:
* Antal agenter – riktighet fel isolering ökar med en ökning av antalet agenter
* antalet hopp i nätverket
* antalet sökvägar mellan käll- och mål

Kunder med högre percentiler (i tabellen ovan), övervaka vanligtvis deras kretsar från flera vantage punkter på sina lokala nätverk.  Flera agenter placeras också djupare i nätverket, längre bort från service provider gränsroutern. Agenterna placeras ofta på flera platser för användaren, filialer och rack i datacenter.

## <a name="service-endpoint-monitor-sepm"></a>Tjänsten Endpoint övervakning (SEPM)

**Vad är avgifter för användning av tjänsten Endpoint övervakning?**

Avgifter för användning av tjänsten Endpoint övervakning beräknas baserat på:
* antalet anslutningar
* mängden data

**Vad är en anslutning?**

En anslutning är ett test av tillgänglighet till en slutpunkt (URL eller network service) från en enskild agent för hela månaden. Till exempel utgör övervakning en anslutning till bing.com från tre agenter tre anslutningar.

**Vad är kostnader för tjänsten Endpoint övervakning?**

- Referera till den [anslutning övervakning](https://azure.microsoft.com/pricing/details/network-watcher/) avsnittet för kostnaden för att övervaka en slutpunkt för hela månaden.
- Kostnad för data som är tillgängligt på den [sida med priser](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.  (Avsnittet: Datapåfyllning).