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
ms.openlocfilehash: 6d93821b59e1f69a48c3d5eeda96dad2edddb188
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="network-monitoring-solutions"></a>Lösningar för nätverksövervakning 

Azure erbjuder en mängd lösningar för att övervaka dina nätverk tillgångar. Azure har lösningar och verktyg för att övervaka nätverksanslutningen, hälsotillståndet för ExpressRoute-kretsar och analysera nätverkstrafik i molnet.

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

Network Performance Monitor (NPM) är en uppsättning funktioner som riktar sig till att övervaka hälsotillståndet för nätverket, nätverksanslutning till dina program och ger insikter om nätverkets prestanda. NPM är molnbaserad och ger en hybrid-nätverket övervakningslösning som övervakar anslutningen mellan:
 
* Molnbaserade distributioner och lokala platser
* Flera datacenter och avdelningskontor
* Mission kritiska flera nivåer program/micro-tjänster
* Användarplatser och webbaserade program (HTTP/HTTPs) 

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

## <a name="next-steps"></a>Nästa steg

* [Konfigurera nätverket Prestandaövervakaren](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [Konfigurera övervakare av nätverksprestanda för ExpressRoute](../expressroute/how-to-npm.md)
