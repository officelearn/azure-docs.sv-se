---
title: Network Performance Monitor-lösning i Azure Log Analytics | Microsoft Docs
description: Använda ExpressRoute Manager-funktion i nätverket Prestandaövervakaren för att övervaka slutpunkt till slutpunkt-anslutning och prestanda mellan dina lokalkontor och Azure, över Azure ExpressRoute.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 9610a8b37ead976cfdfa2fed81d4d3932055ddcc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="expressroute-manager"></a>ExpressRoute Manager

Du kan använda Azure ExpressRoute Manager-funktionen i [Network Performance Monitor](log-analytics-network-performance-monitor.md) att övervaka slutpunkt till slutpunkt-anslutning och prestanda mellan olika avdelningskontor och Azure, över Azure ExpressRoute. Viktiga fördelar är: 

- Automatisk igenkänning av ExpressRoute-kretsar är associerad med din prenumeration.
- Spårning av bandbreddsanvändningen, förluster eller fördröjningar på krets, peering och Azure Virtual Network-nivå för ExpressRoute.
- Identifiering av ExpressRoute-kretsar nätverkstopologi.

![Övervakare för ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Konfiguration 
För att öppna konfigurationen för Network Performance Monitor, öppna den [Network Performance Monitor lösning](log-analytics-network-performance-monitor.md) och välj **konfigurera**.

### <a name="configure-network-security-group-rules"></a>Konfigurera regler för nätverkssäkerhetsgrupper 
För servrar i Azure som används för övervakning via nätverket Prestandaövervakaren, konfigurera regler för nätverkssäkerhetsgrupper (NSG) för att tillåta TCP-trafik på port som används av nätverket Prestandaövervakaren för syntetiska transaktioner. Standardporten är 8084. Den här konfigurationen tillåter Operations Management Suite-agenten som installerats på virtuella Azure-datorer kan kommunicera med en lokal övervakningsagent. 

Mer information om NSG finns [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

>[!NOTE]
> Innan du fortsätter med det här steget installera server-agenten lokalt och Azure-server-agenten och kör EnableRules.ps1 PowerShell-skript. 

 
### <a name="discover-expressroute-peering-connections"></a>Identifiera ExpressRoute-peering anslutningar 
 
1. Välj den **ExpressRoute Peerings** vyn.
2. Välj **identifiera nu** att identifiera alla ExpressRoute privata peerkopplingar som är anslutna till de virtuella nätverken i Azure-prenumeration som är kopplad till den här Azure logganalys-arbetsytan.

    >[!NOTE]
    > Lösningen identifierar för närvarande endast ExpressRoute privata peerkopplingar. 

    >[!NOTE]
    > Endast identifieras privata peerkopplingar som är anslutna till virtuella nätverk som är associerade med prenumerationen som är kopplad till den här logganalys-arbetsytan. Om ExpressRoute är ansluten till virtuella nätverk utanför den prenumeration som är kopplad till den här arbetsytan kan du skapa logganalys-arbetsytan i dessa prenumerationer. Använd sedan Network Performance Monitor för att övervaka de peerkopplingar. 

    ![ExpressRoute Monitor-konfiguration](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 När identifieringen har slutförts visas identifierade privata peering anslutningar i en tabell. Övervakning av dessa peerkopplingar är först i ett inaktiverat tillstånd. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Aktivera övervakning av ExpressRoute-peering anslutningarna 

1. Välj privat peering anslutning som du vill övervaka.
2. Välj i rutan till höger i **övervaka den här Peering** kryssrutan. 
3. Om du tänker skapa hälsa händelser för den här anslutningen väljer **aktivera övervakning av hälsotillstånd för den här peering**. 
4. Välj övervakning villkor. Du kan ange anpassade tröskelvärden för hälsotillstånd händelse genereras genom att ange tröskelvärden. När värdet för villkoret går över dess valda tröskelvärdet för peering-anslutning, skapas en hälsohändelse. 
5. Välj **lägga till agenter** välja övervakningsagenter som du tänker använda för att övervaka den här peering-anslutningen. Kontrollera att du lägger till agenter i båda ändar av anslutningen. Du behöver minst en agent i det virtuella nätverket som är anslutna till den här peering. Du måste också minst en lokal agent ansluten till den här peering. 
6. Välj **spara** att spara konfigurationen. 

   ![Konfiguration av ExpressRoute-övervakning](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


När du aktiverar regler och select-värden och agenter, Vänta 30 till 60 minuter att fylla i värdena och **ExpressRoute övervakning** sammanfattningar som ska visas. När du ser övervakning paneler övervakas nu dina ExpressRoute-kretsar och resurser för anslutning av Network Performance Monitor. 

>[!NOTE]
> Den här funktionen fungerar på ett tillförlitligt sätt på arbetsytor som har uppgraderat till det nya språket i fråga.

## <a name="walkthrough"></a>Genomgång 

Network Performance Monitor-instrumentpanelen visar en översikt över hälsotillståndet för ExpressRoute-kretsar och peering anslutningar. 

![Nätverket Prestandaövervakaren instrumentpanelen](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Kretsar lista 

Om du vill se en lista över alla övervakade ExpressRoute-kretsar, välj panelen ExpressRoute-kretsar. Du kan välja en krets och visa dess hälsotillstånd, trend diagram för paketförlust, bandbreddsanvändning och svarstid. Diagrammen är interaktiva. Du kan välja en anpassad tidsfönstret för att rita upp diagram. Dra muspekaren över ett område på diagrammet för att zooma in och visa detaljerade datapunkter. 

![Lista över ExpressRoute-kretsar](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trender för dataförlust, svarstid och genomströmning 

Diagram för bandbredd användning, svarstid och dataförlust är interaktiva. Du kan zooma in till någon del av dessa diagram med hjälp av musen kontroller. Du kan också se bandbredd, svarstid och förlust av data om du för andra intervall. I det övre vänstra under den **åtgärder** knappen Välj **tidsvärdet**. 

![Svarstid för ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Peerkopplingar lista 

Om du vill visa en lista över alla anslutningar till virtuella nätverk via privat peering, Välj den **privata Peerings** panelen på instrumentpanelen. Här, kan du välja en virtuell nätverksanslutning och visa dess hälsotillstånd, trend diagram för paketförlust, bandbreddsanvändning och svarstid. 

![ExpressRoute peerkopplingar](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Kretsen topologi 

Om du vill visa krets topologi, Välj den **topologi** panelen. Den här åtgärden går du till vyn topologin för den valda krets eller peering. Diagram över topologi ger svarstid för varje segment i nätverket och varje nivå 3-hopp representeras av en nod i diagrammet. Att välja ett hopp visar mer information om hopp. Om du vill öka nivån av synlighet att inkludera lokalt hopp skjutreglaget under **filter**. Flytta skjutreglaget till vänster eller höger ökar eller minskar antalet hopp i diagrammet topologi. Fördröjning mellan varje segment visas, vilket gör att för snabbare isolering av tidsfördröjning segment i nätverket. 

![ExpressRoute-topologi](media/log-analytics-network-performance-monitor/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Topologi för detaljerad vy av en krets 

Den här vyn visar virtuella nätverksanslutningar. 

![ExpressRoute virtuella nätverksanslutningar](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnostik 

Network Performance Monitor hjälper dig att diagnostisera problem med nätverksanslutningen flera krets. Problem i den här listan. 

**Kretsen är avstängd.** Network Performance Monitor meddelar dig när anslutningen mellan din lokala resurser och virtuella Azure-nätverk går förlorad. Det här meddelandet kan du vidta förebyggande åtgärder innan du får användaren eskaleringar och minskar avbrottstiden.

![ExpressRoute-kretsen är nere](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**Trafik som inte passerar genom avsedd krets.** Network Performance Monitor meddelar dig när trafiken inte passerar genom avsedda ExpressRoute-kretsen. Det här problemet kan inträffa om kretsen är nere och trafik som passerar genom säkerhetskopiering vägen. Det kan också inträffa om det finns ett routning problem. Den här informationen hjälper dig att proaktivt hantera alla konfigurationsproblem i din routningsprinciper och se till att de flesta optimalt och säkert vägen. 

 

**Trafik som inte passerar genom primära krets.** Network Performance Monitor meddelar dig när trafik som passerar genom sekundär ExpressRoute-kretsen. Även om du inte får några anslutningsproblem i det här fallet, proaktivt gör felsökning av problem med den primära kretsen du bättre förberedd. 

 
![ExpressRoute trafikflöde](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Prestandaförsämringar på grund av belastning användning.** Du kan jämföra användningstrend bandbredd med trenden svarstid för att identifiera om arbetsbelastning i Azure-försämring beror på en topp bandbreddsanvändningen eller inte. Du kan sedan vidta åtgärder i enlighet med detta.

![Bandbreddsanvändningen för ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Nästa steg
[Söka i loggar](log-analytics-log-searches.md) att visa detaljerad nätverket prestanda dataposter.
