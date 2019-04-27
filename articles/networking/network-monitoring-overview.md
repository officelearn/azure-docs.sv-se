---
title: Om övervakning av nätverk i Azure Monitor loggar | Microsoft Docs
description: Översikt över lösningar, inklusive NPM för att hantera nätverk i molnet, lokalt och hybrid miljöer för nätverksövervakning.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 42fb5d69a1f32d669ad5191e342b3f2f880b8c98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564839"
---
# <a name="network-monitoring-solutions"></a>Lösningar för nätverksövervakning 

Azure erbjuder en mängd lösningar för att övervaka dina nätverk tillgångar. Azure har lösningar och verktyg för att övervaka nätverksanslutningar, hälsotillståndet för ExpressRoute-kretsar och analysera nätverkstrafik i molnet.

## <a name="network-performance-monitor-npm"></a>Övervakare av nätverksprestanda (NPM)

Nätverket prestanda Övervakare (NPM) är en uppsättning funktioner, som riktar sig till hälsoövervakning av nätverket, nätverksanslutning till dina program och ger insikter om nätverkets prestanda. NPM är molnbaserad och tillhandahåller ett hybridnätverk övervakningslösning som övervakar anslutning mellan:
 
* Molnbaserade distributioner och lokala platser
* Flera datacenter och avdelningskontor
* Uppdragskritiska kritiska flerskiktade program/micro-tjänster
* Användarplatser och webbaserade program (HTTP/HTTPs) 

Övervakare av nätverksprestanda, ExpressRoute-övervakning och övervakning av tjänstens anslutning övervakar funktioner i NPM och beskrivs nedan.

## <a name="performance-monitor"></a>Prestandaövervakning

Övervakare av nätverksprestanda ingår i NPM och är övervakning av nätverk för molnet, hybrid och lokala miljöer. Du kan övervaka nätverksanslutningar i filialkontor och lokalkontor, butiker, Datacenter och moln. Du kan identifiera nätverksproblem innan dina användare börjar klaga. Viktiga fördelar är:

* Övervaka förlust och svarstider i alla olika undernät och ställ in aviseringar
* Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket
* Felsök tillfälliga och point-in-time nätverksproblem som är svåra att replikera
* Fastställ det specifika segmentet på nätverket som ligger bakom den försämrade prestandan
* Övervaka nätverkets hälsotillstånd utan behov av SNMP

![NPM topologisk karta](./media/network-monitoring-overview/npm-topology-map.png) 

Mer information kan du läsa följande artiklar:

* [Konfigurera en Network Performance Monitor-lösningen i Azure Monitor-loggar](../azure-monitor/insights/network-performance-monitor.md) 
* [Användningsfall](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Uppdateringar av produkten: [Februari 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [augusti 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute-övervakning

NPM för ExpressRoute erbjuder omfattande ExpressRoute-övervakning för Azure privat peering och Microsoft peering-anslutningar. Du kan övervaka E2E-anslutning och prestanda mellan dina avdelningskontor och Azure via ExpressRoute. De viktigaste funktionerna är:

* Automatisk identifiering av ER kretsar som hör till din prenumeration
* Identifiering av nätverkets topologi från lokalt till molnprogrammen
* Kapacitetsplanering, analys av bandbredd
* Övervakning och avisering för både primära och sekundära sökvägar
* Övervaka anslutningar till Azure-tjänster till exempel Office 365, Dynamics 365... via ExpressRoute
* Identifiera försämring av anslutning till virtuella nätverk

![GEO-karta som visar trafik mellan regioner](./media/network-monitoring-overview/expressroute-topology-map.png) 

Mer information finns i följande artiklar:

* [Konfigurera övervakare av nätverksprestanda för ExpressRoute](../expressroute/how-to-npm.md)
* [Blogginlägg](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Övervakare av tjänstanslutning

Du kan nu testa tillgängligheten för program och identifiera flaskhalsar i prestanda mellan lokala, operatör nätverk och molnet/privat datacenter med övervakning av Tjänstanslutningar.

* Övervaka nätverksanslutningar från slutpunkt till slutpunkt för program
* Korrelera programleverans med nätverksprestanda, identifiera exakta platsen för försämring längs vägen mellan användaren och programmet
* Testa programmet tillgängligheten från flera platser för användare i hela världen
* Fastställa svarstid och paket nätverksförluster för din verksamhet och SaaS-program
* Bestämma aktiva punkter i nätverket, som kan orsaka dålig programprestanda
* Övervaka nätverksåtkomst till Office 365-program med hjälp av inbyggda tester för Microsoft Office 365, Dynamics 365, Skype för företag och andra Microsoft-tjänster

Mer information finns i följande artiklar:

* [Konfigurera Övervakare av nätverksprestanda för övervakning av Tjänsteslutpunkter](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blogginlägg](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Trafikanalys
Trafikanalys är en molnbaserad lösning som ger insyn i användar- och programaktiviteten i dina molnnätverk. NSG-Flödesloggar loggarna har analyserats för att ge insikter om:

* Trafiken flödar över ditt nätverk mellan Azure och Internet, regioner för offentliga moln, virtuella nätverk och undernät
* Program och protokoll i nätverket, utan att behöva Sniffer-program eller dedikerade flow insamlaren installationer
* Toppkommunikatörer, trafikintensiva program, VM-konversationer i molnet, trafik-anslutningar
* Källor och mål för trafik mellan virtuella nätverk, kommunikation mellan relationer mellan affärskritiska processer och program
* Säkerhet – skadlig trafik, portar öppna till Internet, program eller virtuella datorer som försöker Internetåtkomst...
* Kapacitetsutnyttjande - hjälper dig att förhindra problem med behöver etablera eller underutnyttjande genom att övervaka användningstrender för VPN-gatewayer och andra tjänster

Trafikanalys förser dig med användbar information som hjälper att du granskar din organisations nätverksaktivitet, säkra program och data, optimera prestanda för arbetsbelastningen och följ efterlevnad.

![GEO-karta som visar trafik mellan regioner](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Relaterade länkar:
* [Blogginlägget](https://aka.ms/trafficanalytics), [dokumentation](https://aka.ms/trafficanalyticsdocs), [vanliga frågor och svar](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS-analys
Den här lösningen bygger för DNS-administratörer och samlar in, analyserar och korrelerar DNS-loggarna för att tillhandahålla säkerhet, åtgärder och prestandarelaterade insikter.  Några av funktionerna är:

* Identifiering av klienter som försöker matcha skadliga domäner
* Identifiering av inaktuella poster
* Insyn i ofta efterfrågade domännamn och pratsam DNS-klienter
* Insyn i belastning för begäranden på DNS-servrar
* Övervakning av dynamisk DNS-registreringsfel

![DNS Analytics-instrumentpanelen](./media/network-monitoring-overview/dns-analytics-overview.png) 

Relaterade länkar:
* [Blogginlägg](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [Dokumentation](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Övrigt

* [Ny prisnivå](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
