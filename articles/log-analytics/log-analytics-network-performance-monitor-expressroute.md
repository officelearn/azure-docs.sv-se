---
title: "Network Performance Monitor-lösning i Azure Log Analytics | Microsoft Docs"
description: "ExpressRoute Manager-funktion i Prestandaövervakaren i nätverket kan du övervaka slutpunkt till slutpunkt-anslutning och prestanda mellan dina lokalkontor och Azure, över Azure ExpressRoute."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 405bcd7d69e93f838d35b61be489464fcf55ee88
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="expressroute-manager"></a>ExpressRoute Manager

ExpressRoute Manager-funktion i [Network Performance Monitor](log-analytics-network-performance-monitor.md) kan du övervaka slutpunkt till slutpunkt-anslutning och prestanda mellan dina lokalkontor och Azure, över Azure ExpressRoute. Viktiga fördelar är: 

- Automatisk identifiering av ExpressRoute-kretsar som är associerad med din prenumeration 
- Spårning av bandbreddsanvändningen, förluster eller fördröjningar på krets, peering och VNet-nivå för din ExpressRoute 
- Identifiering av ExpressRoute-kretsar nätverkstopologi 

![Övervakare för ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Konfiguration 
För att öppna konfigurationen för Network Performance Monitor, öppna den [Network Performance Monitor lösning](log-analytics-network-performance-monitor.md) och klicka på den **konfigurera** knappen.

### <a name="configure-nsg-rules"></a>Konfigurera NSG-regler 
För servrar i Azure som används för övervakning via NPM, måste du konfigurera regler för nätverkssäkerhetsgrupper (NSG) för att tillåta TCP-trafik på port som används av NPM för syntetiska transaktioner. Standardporten är 8084. Detta gör att OMS-agent installeras på Azure VM att kommunicera med en lokal övervakningsagent. 

Mer information om NSG finns [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

>[!NOTE]
> Kontrollera att du har installerat agenterna (server-agenten lokalt och Azure-server-agent) och har kört EnableRules.ps1 PowerShell-skriptet innan du fortsätter med det här steget. 

 
### <a name="discover-expressroute-peering-connections"></a>Identifiera ExpressRoute-Peering-anslutningar 
 
1. Klicka på den **ExpressRoute Peerings** vyn.  
2. Klicka på den **identifiera nu** för att identifiera alla ExpressRoute privata peerkopplingar som är anslutna till Vnet i Azure-prenumeration som är kopplad till den här logganalys-arbetsytan.  

>[!NOTE]  
> Lösningen identifierar för närvarande endast ExpressRoute privata peerkopplingar. 

>[!NOTE]  
> Endast de privata peerkopplingar identifieras som är anslutna till Vnet som är associerade med prenumerationen som är kopplad till den här logganalys-arbetsytan. Om din ExpressRoute är ansluten till Vnet utanför den prenumeration som är kopplad till den här arbetsytan, måste du skapa en logganalys-arbetsytan i dessa prenumerationer och använda NPM för att övervaka de peerkopplingar. 

 ![Konfigurera ExpressRoute-Övervakaren](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 När identifieringen har slutförts visas identifierade privata peering anslutningar i en tabell. Övervakning av dessa peerkopplingar är inledningsvis i inaktiverat tillstånd. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Aktivera övervakning av ExpressRoute-peering anslutningarna 

1. Klicka på privat peering ansluter du är intresserad övervakning.  
2. I rutan till höger klickar du på kryssrutan för **övervaka den här Peering**. 
3. Om du vill skapa hälsa händelser för den här anslutningen Kontrollera **aktivera övervakning av hälsotillstånd för den här peering**. 
4. Välj övervakning villkor. Du kan ange anpassade tröskelvärden för hälsotillstånd händelse genereras genom att skriva tröskelvärden. När värdet för villkoret går över dess valda tröskelvärdet för peering-anslutning, skapas en hälsohändelse. 
5. Klicka på **lägga till agenter** välja övervakningsagenter som du tänker använda för att övervaka den här peering-anslutningen. Du måste se till att du lägger till agenter på båda ändarna av anslutningen, minst en agent i Azure-VNET som är ansluten till den här peering samt minst en lokal agent ansluten till den här peering. 
6. Klicka på **spara** att spara konfigurationen. 

![Konfigurera ExpressRoute-Övervakaren](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


När du aktiverar regler och välja värden och agenter som du vill övervaka, är en Vänta ungefär 30 till 60 minuter för värden att börja fylla och **ExpressRoute övervakning** paneler ska bli tillgänglig. När du ser övervakning paneler övervakas dina ExpressRoute-kretsar och anslutningen resurser av NPM. 

>[!NOTE]
> Den här funktionen fungerar tillförlitliga på arbetsytor som har uppgraderat till det nya språket i fråga.  

## <a name="walkthrough"></a>Genomgång 

Instrumentpanelen för nätverksövervakning prestanda visar en översikt över hälsotillståndet för ExpressRoute-kretsar och peering anslutningar. 

![NPM instrumentpanelen ExpressRoute](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Kretsar lista 

Om du vill se en lista över alla övervakade ExpressRoute-kretsar klickar du på panelen ExpressRoute-kretsar. Du kan välja en krets och visa dess hälsotillstånd, trend diagram för paketförlust, bandbreddsanvändning och svarstid. Diagrammen är interaktiva. Du kan välja en anpassad tidsfönstret för att rita upp diagram. Du kan dra muspekaren över ett område på diagrammet för att zooma in och visa detaljerade datapunkter. 

![Lista över ExpressRoute-kretsar](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trend-of-loss-latency-and-throughput"></a>Trend för dataförlust, svarstid och genomströmning 

Diagram för bandbredd, svarstid och dataförlust är interaktiva. Du kan zooma in en del av dessa diagram med hjälp av musen kontroller. Du kan också se bandbredd, svarstid och förlust av data för andra intervall genom att klicka på datum/tid, finns under åtgärder-knappen längst upp till vänster. 

![Svarstid för ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Peerkopplingar lista 

Klicka på den **privata Peerings** panel på instrumentpanelen visar en lista över alla anslutningar till virtuella nätverk via privat peering. Här, kan du välja en virtuell nätverksanslutning och visa dess hälsotillstånd, trend diagram för paketförlust, bandbreddsanvändning och svarstid. 

![ExpressRoute Peerkopplingar](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Kretsen topologi 

Om du vill visa krets topologi, klicka på den **topologi** panelen. Då kommer du till vyn topologin för den valda krets eller peering. Diagram över topologi ger svarstid för varje segment i nätverket och varje nivå 3-hopp representeras av en nod i diagrammet. Klicka på ett hopp visar mer information om hopp. Du kan öka synlighet att inkludera lokalt hopp genom att flytta skjutreglaget nedan **filter**. Flytta skjutreglaget till vänster eller höger, ökar/minskar antalet hopp i diagrammet topologi. Fördröjning mellan varje segment visas, vilket gör att för snabbare isolering av fördröjningar segment i nätverket. 

![ExpressRoute-topologi](media/log-analytics-network-performance-monitor/expressroute-topology.png)  

### <a name="detailed-topology-view-of-a-circuit"></a>Detaljerad topologi vy av en krets 

Den här vyn visar VNet-anslutningar. 

![ExpressRoute-VNet](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnostik 

Network Performance Monitor hjälper dig att diagnostisera problem med nätverksanslutningen flera krets. Vissa av problem som anges nedan 

**Kretsen är avstängd.** NPM meddelar dig när anslutningen mellan din lokala resurser och Azure Vnet går förlorad. Det hjälper dig att vidta förebyggande åtgärder innan du tar emot förfrågningar för användaren och minska avbrottstiden 

![ExpressRoute-krets ned](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**Trafik som inte passerar genom avsedd krets.** NPM kan meddela dig när trafiken oväntat inte passerar genom avsedda ExpressRoute-kretsen. Detta kan inträffa om kretsen är nere och trafiken som passerar genom säkerhetskopiering vägen eller om det finns ett problem med routning. Den här informationen hjälper dig att proaktivt hantera alla konfigurationsproblem i din routningsprinciper och kontrollera att de flesta optimalt och säkert flödet används. 

 

**Trafik som inte passerar genom primära krets.** Kapaciteten meddelar dig när trafiken som passerar genom sekundär ExpressRoute-kretsen. Även om du inte får några problem med nätverksanslutningen i det här fallet, men proaktivt felsöker problem med den primära kretsen får du bättre förberedda. 

 
![ExpressRoute trafikflöde](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Prestandaförsämringar på grund av belastning användning.** Du kan korrelera användningstrend bandbredd med trenden svarstid för att identifiera om arbetsbelastning i Azure-försämring beror på en topp bandbreddsanvändningen eller inte och vidta åtgärder i enlighet med detta.  

![Övervakare för ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Nästa steg
* [Söka i loggar](log-analytics-log-searches.md) att visa detaljerad nätverket prestanda dataposter.
