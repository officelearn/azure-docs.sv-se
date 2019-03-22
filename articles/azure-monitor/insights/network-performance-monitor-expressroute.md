---
title: Network Performance Monitor-lösningen i Azure Log Analytics | Microsoft Docs
description: Använd funktionen för ExpressRoute-övervakning i Övervakare av nätverksprestanda för att övervaka anslutningar slutpunkt till slutpunkt och prestanda mellan dina avdelningskontor och Azure, via Azure ExpressRoute.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: abshamsft
ms.openlocfilehash: d0819b57307fc037b3be6ab04ed9ec6c8720a618
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103595"
---
# <a name="expressroute-monitor"></a>ExpressRoute-övervakning

Du kan använda funktionen för Azure ExpressRoute-övervakning [Övervakare av nätverksprestanda](network-performance-monitor.md) att övervaka anslutningar slutpunkt till slutpunkt och prestanda mellan dina avdelningskontor och Azure, via Azure ExpressRoute. Viktiga fördelar är: 

- Autoidentifiering av ExpressRoute-kretsar som är associerade med prenumerationen.
- Spårning av bredbandsanvändning, förlust och svarstid i kretsen, peering och Azure Virtual Network-nivå för ExpressRoute.
- Identifiering av nätverkstopologin för ExpressRoute-kretsarna.

![ExpressRoute-övervakning](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfiguration 
För att öppna konfigurationen för Övervakare av nätverksprestanda, öppna den [Network Performance Monitor-lösningen](network-performance-monitor.md) och välj **konfigurera**.

### <a name="configure-network-security-group-rules"></a>Konfigurera regler för nätverkssäkerhetsgrupper 
För servrar i Azure som används för övervakning via Övervakare av nätverksprestanda, kan du konfigurera regler för nätverkssäkerhetsgrupper (NSG) för att tillåta TCP-trafik på porten som används av Övervakare av nätverksprestanda för syntetiska transaktioner. Standardporten är 8084. Den här konfigurationen kan Log Analytics-agenten installeras på Azure virtuella datorer ska kunna kommunicera med en lokal övervakningsagent. 

Mer information om Nätverkssäkerhetsgrupper finns i [Nätverkssäkerhetsgrupper](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Innan du fortsätter med det här steget kan installera en lokal server-agenten och Azure server-agenten och kör EnableRules.ps1 PowerShell-skript. 

 
### <a name="discover-expressroute-peering-connections"></a>Identifiera peering ExpressRoute-anslutningar 
 
1. Välj den **ExpressRoute Peerings** vy.
2. Välj **identifiera nu** att identifiera alla ExpressRoute privat peering-sessioner som är anslutna till de virtuella nätverken i Azure-prenumeration som är kopplad till den här Azure Log Analytics-arbetsytan.

    >[!NOTE]
    > Lösningen identifierar för närvarande endast ExpressRoute privat peering-sessioner. 

    >[!NOTE]
    > Endast identifieras privata peerkopplingar som är anslutna till virtuella nätverk som är associerade med prenumerationen är kopplad till den här Log Analytics-arbetsytan. Om ExpressRoute är ansluten till virtuella nätverk utanför den prenumeration som är länkad till den här arbetsytan kan du skapa en Log Analytics-arbetsyta i dessa prenumerationer. Använd sedan Övervakare av nätverksprestanda för att övervaka de peer-kopplingar. 

    ![Konfiguration av ExpressRoute-övervakning](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   När identifieringen är klar visas de identifierade privata peering-anslutningarna i en tabell. Övervakning för dessa peer-kopplingar är först i ett inaktiverat tillstånd. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Aktivera övervakning av ExpressRoute-peering-anslutningar 

1. Välj den privata peering-anslutningen som du vill övervaka.
2. I rutan till höger väljer den **övervaka denna Peering** markerar du kryssrutan. 
3. Om du tänker skapa health-händelser för den här anslutningen väljer **aktivera övervakning av hälsotillstånd för denna peering**. 
4. Välj övervakning villkor. Du kan ange anpassade tröskelvärden för generering av fönsterhändelse hälsa genom att ange tröskelvärden. När värdet för villkoret går över det valda tröskelvärdet för peering-anslutningen, genereras en hälsotillståndshändelse. 
5. Välj **Lägg till agenter** välja övervakningsagenter som du planerar att använda för att övervaka denna peering-anslutningen. Se till att du lägger till agenter i bägge ändar av anslutningen. Du behöver minst en agent i det virtuella nätverket som är anslutna till den här peeringen. Du måste också minst en lokal agent som är anslutna till den här peeringen. 
6. Välj **spara** att spara konfigurationen. 

   ![Konfiguration av ExpressRoute-övervakning](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


När du har aktiverat de regler och select-värden och agenter Vänta 30 till 60 minuter att fylla i värdena och **ExpressRoute-övervakning** paneler visas. När du ser övervakning panelerna övervakas din ExpressRoute-kretsar och anslutningsresurser nu av Övervakare av nätverksprestanda. 

>[!NOTE]
> Den här funktionen fungerar på ett tillförlitligt sätt på arbetsytor som har uppgraderat till det nya frågespråket.

## <a name="walkthrough"></a>Genomgång 

Network Performance Monitor-instrumentpanelen visar en översikt över hälsotillståndet för ExpressRoute-kretsar och peering-anslutningar. 

![Network Performance Monitor-instrumentpanel](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Kretsar lista 

Om du vill se en lista över alla övervakade ExpressRoute-kretsar, väljer du panelen ExpressRoute-kretsar. Du kan välja en krets och visa dess hälsotillstånd, trenddiagram för paketförlust, nyttjandet av bandbredd och latens. Diagrammen är interaktiva. Du kan välja ett anpassat tidsintervall för diagrammen. Dra musen över ett område i diagrammet att zooma in och se detaljerade datapunkter. 

![ExpressRoute-kretsar lista](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trender för dataförlust, svarstid och dataflöde 

Bandbredd användning, svarstid och förlust diagram är interaktiva. Du kan zooma in till ett visst avsnitt dessa diagram med hjälp av musen kontroller. Du kan också se den bandbredd, svarstid och förlust av data om du för andra intervall. I det övre vänstra hörnet under den **åtgärder** knapp, väljer **datum/tid**. 

![ExpressRoute-svarstid](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Peerkopplingar lista 

Om du vill ta fram en lista över alla anslutningar till virtuella nätverk över privat peering, Välj den **privat Peerings** panelen på instrumentpanelen. Här kan du kan välja en virtuell nätverksanslutning och visa dess hälsotillstånd, trenddiagram för paketförlust, nyttjandet av bandbredd och latens. 

![ExpressRoute-peerkopplingar](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Kretsen topologi 

Om du vill visa krets topologi, Välj den **topologi** panelen. Den här åtgärden tar dig till vyn topologin för den valda krets eller -peering. Diagram över topologi ger svarstiden för varje segment i nätverket och varje layer 3-hopp representeras av en nod i diagrammet. Att välja ett hopp visar mer information om hoppet. Om du vill öka nivån av synlighet att inkludera den lokala hopp skjutreglaget under **filter**. Flytta skjutreglaget till vänster eller höger ökar eller minskar antalet hopp i diagrammet topologi. Fördröjning mellan varje segment är synliga, vilket gör att för snabbare isolering med lång svarstid segment i nätverket.

![ExpressRoute-topologi](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Topologi för detaljerad vy av en krets 

Den här vyn visar virtuella nätverksanslutningar. 

![ExpressRoute-anslutningar för virtuellt nätverk](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostik 

Övervakare av nätverksprestanda hjälper dig att diagnostisera anslutningsproblem för flera krets. Några av de problem som du kan se listas nedan.

Du kan se Meddelandekoderna och ställa in aviseringar på dem via **LogAnalytics**. På den **NPM diagnostik** kan du se beskrivningar för varje diagnostik-meddelande utlöst.

| Meddelandekod (loggar) | Beskrivning |
| --- | --- |
| 5501 | Det går inte att passera genom den sekundära anslutningen av ExpressRoute-krets |
| 5502 | Det går inte att passera genom den primära anslutningen av ExpressRoute-krets |
| 5503 | Ingen krets hittades för den prenumeration som är länkat till arbetsytan | 
| 5508 | Det går inte att fastställa om trafiken passerar genom några kretsar för sökvägen |
| 5510 | Trafiken passerar inte genom den avsedda kretsen | 
| 5511 | Trafiken passerar inte genom det avsedda virtuella nätverket | 

**Kretsen har stoppats.** Övervakare av nätverksprestanda meddelar dig så snart anslutningen mellan dina lokala resurser och Azure-nätverk går förlorad. Det här meddelandet kan du vidta proaktiva åtgärder innan du får användaren kundproblem och minskade tider för driftstopp.

![ExpressRoute-kretsen är nere](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Trafik som passerar inte genom avsedda kretsen.** Övervakare av nätverksprestanda meddelar dig när trafiken inte är passerar genom den avsedda ExpressRoute-kretsen. Det här problemet kan inträffa om kretsen är nere och trafik som flödar genom säkerhetskopiering vägen. Det kan också inträffa om det finns ett routningsproblem. Den här informationen hjälper dig att proaktivt hantera eventuella konfigurationsproblem i dina principer för Routning och se till att den mest optimala och säker vägen används. 

 

**Trafik som passerar inte genom primära kretsen.** Övervakare av nätverksprestanda meddelar dig när trafik som flödar genom den sekundära ExpressRoute-kretsen. Även om det inte uppstår några problem med nätverksanslutningen i det här fallet, proaktivt gör felsökning av problem med den primära kretsen dig bättre förberedd. 

 
![ExpressRoute trafikflöde](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Prestandaförsämring på grund av högsta användning.** Du kan jämföra användningstrend bandbredd med svarstid trenden att identifiera om Azure-arbetsbelastning försämringen är på grund av en topp i bandbreddsanvändningen eller inte. Du kan sedan vidta åtgärder i enlighet med detta.

![Bandbreddsanvändningen för ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Nästa steg
[Söka loggarna](../../azure-monitor/log-query/log-query-overview.md) att visa detaljerad nätverk prestanda dataposter.
