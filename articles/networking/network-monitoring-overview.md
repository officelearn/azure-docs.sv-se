---
title: "Om nätverksövervakning i logganalys | Microsoft Docs"
description: "Översikt över lösningar, inklusive NPM, för att hantera nätverk över molnet, lokalt och hybrid-miljöer för nätverksövervakning."
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 7b9f42607f313f5570f414e810eafc6775ea18b9
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="network-monitoring-solutions"></a>Lösningar för nätverksövervakning 

Azure erbjuder en mängd lösningar för att övervaka dina nätverk tillgångar. Azure har lösningar och verktyg för att övervaka nätverksanslutningen, hälsotillståndet för ExpressRoute-kretsar och analysera nätverkstrafik i molnet.

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

Network Performance Monitor (NPM) är en uppsättning funktioner som riktar sig till att övervaka hälsotillståndet för nätverket, nätverksanslutning till dina program och ger insikter om nätverkets prestanda. NPM är molnbaserad och ger en hybrid-nätverket övervakningslösning som övervakar anslutningen mellan:
 
* Molnbaserade distributioner och lokala platser
* Flera datacenter och avdelningskontor
* Mission kritiska flera nivåer program/micro-tjänster
* Användarplatser och webbaserade program (HTTP/HTTPs) 

Prestandaövervakaren, ExpressRoute-övervakaren och tjänsten Endpoint övervakning övervakar funktioner i NPM och beskrivs nedan.

## <a name="performance-monitor"></a>Prestandaövervakning

Prestandaövervakaren är en del av NPM network Monitor för moln, hybridmoln och lokala miljöer. Du kan övervaka nätverksanslutningen mellan fjärranslutna avdelningskontor och filialer, lagringsplatser, Datacenter och moln. Du kan identifiera problem innan dina användare klagar. Viktiga fördelar är:

* Övervaka förluster eller fördröjningar över olika undernät och set-aviseringar
* Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket
* Felsökning av nätverksproblem med tillfälliga och tidpunkt i, som är svåra att replikera
* Fastställa specifika segmentet i nätverket, som ansvarar för försämrade prestanda
* Övervakning av hälsan på nätverket, utan att behöva SNMP

![NPM topologisk karta](./media/network-monitoring-overview/npm-topology-map.png) 

Mer information läser du följande artiklar:

* [Konfigurera en nätverkslösning Performance Monitor i logganalys](../log-analytics/log-analytics-network-performance-monitor.md) 
* [Användningsfall](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Produktuppdateringar: [februari 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [augusti 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Övervakare för ExpressRoute

NPM expressroute ger fullständig övervakning för privat peering anslutningar på ExpressRoute. Du kan övervaka E2E anslutning och prestanda mellan dina lokalkontor och Azure via ExpressRoute. De viktigaste funktionerna är:

* Automatisk identifiering av ER kretsar som är associerad med din prenumeration
* Identifiering av nätverkets topologi från lokal till dina molnprogram
* Kapacitetsplanering användning analys
* Övervakning och avisering på både primära och sekundära sökvägar
* Identifiera försämring av anslutning till Vnet

Mer information finns i följande artiklar:

* [Konfigurera övervakare av nätverksprestanda för ExpressRoute](../expressroute/how-to-npm.md)
* [blogginlägget](https://aka.ms/NPMExRmonitorGA)

## <a name="service-endpoint-monitor"></a>Tjänsten Endpoint för övervakning

Med tjänstslutpunkten övervakning, kan du nu testa tillgängligheten för program och identifiera prestandaflaskhalsar via lokalt, operatör nätverk och moln/privat datacenter.

* Övervaka slutpunkt till slutpunkt nätverksanslutning till program
* Korrelera leverans av program med nätverksprestanda, identifiera exakta platsen för försämring längs vägen mellan användaren och programmet
* Testa programmet tillgängligheten från flera användarplatser över hela världen
* Fastställa latens och paket nätverksförluster för ditt företag och SaaS-program
* Bestämma aktiva punkter i nätverket, som kan orsaka dåliga programmens prestanda
* Övervaka tillgängligheten till Office 365-program med hjälp av inbyggda tester för Microsoft Office 365, Dynamics 365 Skype för företag och andra Microsoft-tjänster

Mer information finns i följande artiklar:

* [Konfigurera nätverket Prestandaövervakaren för övervakning av slutpunkter](https://aka.ms/applicationconnectivitymonitorguide)
* [blogginlägget](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Trafikanalys
Trafik Analytics är en molnbaserad lösning som ger inblick i användar- och programaktivitet på ditt nätverk i molnet. NSG flöda loggarna har analyserats för att ge insikter om:

* Trafiken flödar över ditt nätverk mellan Azure och Internet, offentligt moln regioner, Vnet och undernät
* Program och protokoll i nätverket, utan att behöva Sniffer-program eller dedikerade flödet insamlaren installationer
* De främsta talkers, chatty program, Virtuella konversationer i molnet, trafik surfpunkter
* Källor eller mål för trafik mellan virtuella nätverk, mellan relationer mellan affärskritiska tjänster och program
* Säkerhet – skadlig trafik, portar öppna till Internet, program eller virtuella datorer som försöker Internetåtkomst...
* Kapacitetsutnyttjande - hjälper dig att undvika problem överbelasta eller underutnyttjande genom att övervaka trender för användningen av VPN-gatewayer och andra tjänster

Trafik Analytics är tillämplig information som hjälper till att du granskar din organisations nätverksaktivitet, säkra program och data, optimera arbetsbelastningsprestanda och vara kompatibla.

![GEO-karta visar trafik över regioner](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Relaterade länkar:
* [Blogginlägget](https://aka.ms/trafficanalytics), [dokumentationen](https://aka.ms/trafficanalyticsdocs), [vanliga frågor och svar](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS-analys
Den här lösningen bygger för DNS-administratörer samlar in, analyserar och korrelerar DNS-loggarna för att tillhandahålla säkerhet, åtgärder och prestandarelaterade insikter.  Några av funktionerna är:

* Identifiering av klienter som försöker matcha skadliga domäner
* Identifiering av inaktuella poster
* Inblick i ofta efterfrågade domännamn och talkative DNS-klienter
* Överblick av belastningen på DNS-servrar
* Övervakning av dynamisk DNS-registreringsfel

![DNS Analytics Dashboard](./media/network-monitoring-overview/dns-analytics-overview.png) 

Relaterade länkar:
* [Blogginlägget](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [dokumentation](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera nätverket Prestandaövervakaren](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [Konfigurera övervakare av nätverksprestanda för ExpressRoute](../expressroute/how-to-npm.md)
