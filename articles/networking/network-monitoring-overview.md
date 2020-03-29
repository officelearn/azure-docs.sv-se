---
title: Om nätverksövervakning i Azure Monitor-loggar | Microsoft-dokument
description: Översikt över nätverksövervakningslösningar, inklusive NPM, för att hantera nätverk i moln-, lokala miljöer och hybridmiljöer.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: 2912488286745bf8d2e567d09e445b0a44dc7c39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67672187"
---
# <a name="network-monitoring-solutions"></a>Lösningar för nätverksövervakning 

Azure erbjuder en mängd lösningar för att övervaka dina nätverkstillgångar. Azure har lösningar och verktyg för att övervaka nätverksanslutning, hälsotillståndet för ExpressRoute-kretsar och analysera nätverkstrafik i molnet.

## <a name="network-performance-monitor-npm"></a>Övervakare av nätverksprestanda (NPM)

NPM (Network Performance Monitor) är en uppsättning funktioner som var och en är inriktad på att övervaka nätverkets hälsa, nätverksanslutning till dina program och ger insikter om nätverkets prestanda. NPM är molnbaserat och tillhandahåller en hybridnätverksövervakningslösning som övervakar anslutningen mellan:
 
* Molndistributioner och lokala platser
* Flera datacenter och filialkontor
* Verksamhetskritiska flernivåapplikationer/mikrotjänster
* Användarplatser och webbaserade program (HTTP/HTTPs) 

Performance Monitor, ExpressRoute Monitor och Service Connectivity Monitor övervakar funktioner inom NPM och beskrivs nedan.

## <a name="performance-monitor"></a>Prestandaövervakaren

Performance Monitor är en del av NPM och är nätverksövervakning för moln-, hybrid- och lokala miljöer. Du kan övervaka nätverksanslutningen över fjärrkontor och fältkontor, lagra platser, datacenter och moln. Du kan identifiera nätverksproblem innan användarna klagar. De viktigaste fördelarna är:

* Övervaka förlust och svarstid i olika undernät och ställa in aviseringar
* Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket
* Felsöka tillfälliga och punkt-i-tid nätverksproblem, som är svåra att replikera
* Bestäm det specifika segmentet i nätverket, som ansvarar för försämrad prestanda
* Övervaka nätverkets hälsa, utan att det behövs SNMP

![NPM-topologi karta](./media/network-monitoring-overview/npm-topology-map.png) 

Mer information finns i följande artiklar:

* [Konfigurera en lösning för nätverksprestandaövervakare i Azure Monitor-loggar](../azure-monitor/insights/network-performance-monitor.md) 
* [Användningsfall](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
* Produktuppdateringar:
  * [Februari 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/)
  * [Augusti 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute-övervakare

NPM för ExpressRoute erbjuder omfattande ExpressRoute-övervakning för Azure Private peering och Microsoft peering-anslutningar. Du kan övervaka E2E-anslutning och prestanda mellan dina filialkontor och Azure via ExpressRoute. De viktigaste funktionerna är:

* Automatisk identifiering av ER-kretsar som är associerade med din prenumeration
* Identifiering av nätverkstopologi från lokalt till dina molnprogram
* Kapacitetsplanering, bandbreddsutnyttjandeanalys
* Övervakning och avisering på både primära och sekundära sökvägar
* Övervaka anslutning till Azure-tjänster som Office 365, Dynamics 365, ... över ExpressRoute
* Identifiera försämring av anslutningen till virtuella nätverk

![Geokarta som visar trafik mellan regioner](./media/network-monitoring-overview/expressroute-topology-map.png) 

Mer information finns i följande artiklar:

* [Konfigurera övervakare av nätverksprestanda för ExpressRoute](../expressroute/how-to-npm.md)
* [Blogginlägg](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Övervakare av tjänstanslutning

Med övervakning av tjänstanslutning kan du nu testa programens räckvidd och upptäcka flaskhalsar i prestanda i lokala nätverk, operatörsnätverk och moln/privata datacenter.

* Övervaka nätverksanslutning från slutpunkt till slutpunkt till program
* Korrelera programleverans med nätverksprestanda, identifiera exakt plats för nedbrytning längs sökvägen mellan användaren och programmet
* Testa programmets nåbarhet från flera användarplatser över hela världen
* Fastställ nätverksfördröjning och paketförlust för din bransch och SaaS-program
* Bestäm aktiva punkter i nätverket, som kan orsaka dålig programprestanda
* Övervaka räckvidden för Office 365-program med hjälp av inbyggda tester för Microsoft Office 365, Dynamics 365, Skype för företag och andra Microsoft-tjänster

Mer information finns i följande artiklar:

* [Konfigurera nätverksprestandaövervakaren för övervakning av tjänstslutpunkter](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blogginlägg](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Trafikanalys
Traffic Analytics är en molnbaserad lösning som ger insyn i användar- och programaktivitet i dina molnnätverk. NSG Flow loggar analyseras för att ge insikter i:

* Trafikflöden över dina nätverk mellan Azure och Internet, offentliga molnregioner, virtuella nätverk och undernät
* Program och protokoll i nätverket, utan behov av sniffers eller dedikerade flödesinsamlare
* Top talare, pratsamma program, VM-konversationer i molnet, trafik hotspots
* Källor och trafikdestinationer över VNETs, relationer mellan kritiska affärstjänster och applikationer
* Säkerhet – skadlig trafik, portar som är öppna för Internet, program eller virtuella datorer som försöker komma åt Internet...
* Kapacitetsutnyttjande - hjälper dig att eliminera problem med överetablering eller underutnyttjande genom att övervaka användningstrender för VPN-gateways och andra tjänster

Traffic Analytics förser dig med användbar information som hjälper dig att granska organisationens nätverksaktivitet, säkra program och data, optimera arbetsbelastningsprestanda och hålla dig kompatibel.

![Geokarta som visar trafik mellan regioner](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Relaterade länkar:
* [Blogginlägg](https://aka.ms/trafficanalytics), [Dokumentation](https://aka.ms/trafficanalyticsdocs), [Vanliga frågor och svar](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS-analys
Den här lösningen är byggd för DNS-administratörer och samlar in, analyserar och korrelerar DNS-loggar för att tillhandahålla säkerhets-, åtgärders- och prestandarelaterade insikter.  Några av funktionerna är:

* Identifiering av klienter som försöker lösa till skadliga domäner
* Identifiering av inaktuella resursposter
* Insyn i ofta efterfrågade domännamn och pratsamma DNS-klienter
* Insyn i begäranden på DNS-servrar
* Övervakning av dynamiska DNS-registreringsfel

![Instrumentpanel för DNS-analys](./media/network-monitoring-overview/dns-analytics-overview.png) 

Relaterade länkar:
* [Blogginlägg](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [Dokumentation](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Övrigt

* [Ny prissättning](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
