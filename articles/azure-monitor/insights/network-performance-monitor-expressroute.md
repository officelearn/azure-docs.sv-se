---
title: Lösning för nätverksprestandaövervakare i Azure Log Analytics | Microsoft-dokument
description: Använd ExpressRoute Monitor-funktionen i Network Performance Monitor för att övervaka heltäckande anslutning och prestanda mellan dina filialkontor och Azure, via Azure ExpressRoute.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 6ac610d7dcf9849b6b439741957684867b9d01aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660758"
---
# <a name="expressroute-monitor"></a>ExpressRoute-övervakare

Du kan använda Azure ExpressRoute Monitor-funktionen i [Network Performance Monitor](network-performance-monitor.md) för att övervaka heltäckande anslutning och prestanda mellan dina filialkontor och Azure, via Azure ExpressRoute. De viktigaste fördelarna är: 

- Automatisk avdetection av ExpressRoute-kretsar som är associerade med din prenumeration.
- Spårning av bandbreddsanvändning, förlust och svarstid på krets-, peer-nivå och Azure Virtual Network-nivå för ExpressRoute.
- Identifiering av nätverkstopologi för dina ExpressRoute-kretsar.

![ExpressRoute-övervakare](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfiguration 
Om du vill öppna konfigurationen för Network Performance Monitor öppnar du [lösningen För nätverksprestandaövervakare](network-performance-monitor.md) och väljer **Konfigurera**.

### <a name="configure-network-security-group-rules"></a>Konfigurera regler för nätverkssäkerhetsgrupper 
Konfigurera NSG-regler (Network Security Group) för servrar i Azure som används för övervakning via Network Performance Monitor så att TCP-trafik tillåts på porten som används av Network Performance Monitor för syntetiska transaktioner. Standardporten är 8084. Med den här konfigurationen kan Log Analytics-agenten som är installerad på virtuella Azure-datorer kommunicera med en lokal övervakningsagent. 

Mer information om NSG finns i [Nätverkssäkerhetsgrupper](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Innan du fortsätter med det här steget installerar du den lokala serveragenten och Azure-serveragenten och kör PowerShell-skriptet EnableRules.ps1. 

 
### <a name="discover-expressroute-peering-connections"></a>Upptäck ExpressRoute-peering-anslutningar 
 
1. Välj **vyn ExpressRoute-peerings.**
2. Välj **Upptäck nu** om du vill identifiera alla privata ExpressRoute-peerings som är anslutna till de virtuella nätverken i Azure-prenumerationen som är länkad till den här Azure Log Analytics-arbetsytan.

    >[!NOTE]
    > Lösningen identifierar för närvarande endast ExpressRoute privata peerings. 

    >[!NOTE]
    > Endast privata peerings som är anslutna till de virtuella nätverk som är associerade med prenumerationen som är länkade till den här logganalysarbetsytan identifieras. Om ExpressRoute är anslutet till virtuella nätverk utanför prenumerationen som är länkad till den här arbetsytan skapar du en Log Analytics-arbetsyta i dessa prenumerationer. Använd sedan Network Performance Monitor för att övervaka dessa peerings. 

    ![Konfiguration av ExpressRoute-övervakare](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   När identifieringen är klar visas de identifierade privata peering-anslutningarna i en tabell. Övervakningen för dessa peerings är ursprungligen i ett inaktiverat tillstånd. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Aktivera övervakning av ExpressRoute-peering-anslutningar 

1. Välj den privata peering-anslutning som du vill övervaka.
2. Markera kryssrutan Övervaka den **här peering-rutan i** fönstret till höger. 
3. Om du tänker skapa hälsohändelser för den här anslutningen väljer du **Aktivera hälsoövervakning för den här peer-peer-datorn**. 
4. Välj övervakningsvillkor. Du kan ange anpassade tröskelvärden för hälsohändelsegenerering genom att ange tröskelvärden. När värdet för villkoret överskrider det valda tröskelvärdet för peering-anslutningen genereras en hälsohändelse. 
5. Välj **Lägg till agenter om** du vill välja de övervakningsagenter som du tänker använda för att övervaka den här peering-anslutningen. Se till att du lägger till agenter i båda ändarna av anslutningen. Du behöver minst en agent i det virtuella nätverket som är anslutet till den här peer-datorn. Du behöver också minst en lokal agent som är ansluten till den här peeringen. 
6. Välj **Spara** om du vill spara konfigurationen. 

   ![ExpressRoute övervakningskonfiguration](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


När du har aktiverat reglerna och valt värden och agenter väntar du 30 till 60 minuter på att värdena ska fyllas i och att **ExpressRoute Monitoring-panelerna** ska visas. När övervakningspanelerna visas övervakas nu Dina ExpressRoute-kretsar och anslutningsresurser av Network Performance Monitor. 

>[!NOTE]
> Den här funktionen fungerar tillförlitligt på arbetsytor som har uppgraderat till det nya frågespråket.

## <a name="walkthrough"></a>Genomgång 

Instrumentpanelen Nätverksprestandaövervakare visar en översikt över hälsotillståndet för ExpressRoute-kretsar och peering-anslutningar. 

![Instrumentpanelen Nätverksprestandaövervakare](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Kretslistan 

Om du vill se en lista över alla övervakade ExpressRoute-kretsar väljer du panelen ExpressRoute-kretsar. Du kan välja en krets och visa dess hälsotillstånd, trenddiagram för paketförlust, bandbreddsanvändning och svarstid. Diagrammen är interaktiva. Du kan välja ett anpassat tidsfönster för att rita diagrammen. Dra musen över ett område i diagrammet för att zooma in och se detaljerade datapunkter. 

![ExpressRoute kretsar lista](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trender för förlust, latens och dataflöde 

Diagrammen för bandbreddsanvändning, svarstid och förlust är interaktiva. Du kan zooma in på valfri del av dessa diagram med hjälp av muskontroller. Du kan också se bandbredd, svarstid och förlustdata för andra intervall. Välj **Datum/tid**längst upp till vänster under knappen **Åtgärder** . 

![Svarstid för ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Peerings lista 

Om du vill visa en lista över alla anslutningar till virtuella nätverk via privat peering väljer du panelen **Privata peerings** på instrumentpanelen. Här kan du välja en virtuell nätverksanslutning och visa dess hälsotillstånd, trenddiagram för paketförlust, bandbreddsutnyttjande och svarstid. 

![ExpressRoute-peerings](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Kretstopologi 

Om du vill visa kretstopologin väljer du panelen **Topologi.** Den här åtgärden tar dig till topologivyn för den valda kretsen eller peering. Topologidiagrammet ger svarstiden för varje segment i nätverket och varje lager 3-hopp representeras av en nod i diagrammet. Om du väljer ett hopp visas mer information om hoppet. Om du vill öka synlighetsnivån så att den inkluderar lokala hopp flyttar du skjutreglaget under **FILTER**. Om du flyttar skjutreglaget åt vänster eller höger ökar eller minskar antalet hopp i topologidiagrammet. Svarstiden för varje segment är synlig, vilket möjliggör snabbare isolering av hög latenssegment i nätverket.

![ExpressRoute topologi](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Detaljerad topologi bild av en krets 

I den här vyn visas virtuella nätverksanslutningar. 

![Virtuella expressroute-anslutningar](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostik 

Network Performance Monitor hjälper dig att diagnostisera flera problem med kretsanslutningen. Några av de problem som du kan se visas nedan.

Du kan se meddelandekoder och ställa in aviseringar på dem via **LogAnalytics**. På sidan **NPM-diagnostik** kan du se beskrivningar för varje diagnostikmeddelande som utlöses.

| Meddelandekod (loggar) | Beskrivning |
| --- | --- |
| 5501 | Det går inte att passera via sekundär anslutning av ExpressRoute-kretsen |
| 5502 | Det går inte att passera genom primär anslutning av ExpressRoute-kretsen |
| 5503 | Det finns ingen krets för prenumeration kopplad till arbetsytan | 
| 5508 | Inte kunna avgöra om trafiken passerar genom någon krets (er) för väg |
| 5510 | Trafiken passerar inte genom den avsedda kretsen | 
| 5511 | Trafiken passerar inte genom det avsedda virtuella nätverket | 

**Kretsen är nere.** Network Performance Monitor meddelar dig så fort anslutningen mellan dina lokala resurser och virtuella Azure-nätverk går förlorad. Det här meddelandet hjälper dig att vidta proaktiva åtgärder innan du får eskalering av användare och minskar stilleståndstiden.

![ExpressRoute kretsen är nere](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Trafiken som inte strömmar genom avsedd krets.** Network Performance Monitor meddelar dig när trafiken inte flödar genom den avsedda ExpressRoute-kretsen. Det här problemet kan inträffa om kretsen är nere och trafiken flödar genom säkerhetskopieringsvägen. Det kan också hända om det finns en routning fråga. Den här informationen hjälper dig att proaktivt hantera eventuella konfigurationsproblem i dina routningsprinciper och se till att den mest optimala och säkra vägen används. 

 

**Trafiken som inte flyter genom primärkretsen.** Network Performance Monitor meddelar dig när trafiken flödar genom den sekundära ExpressRoute-kretsen. Även om du inte kommer att uppleva några anslutningsproblem i det här fallet, proaktivt felsökning av problem med den primära kretsen gör att du bättre förberedda. 

 
![ExpressRoute trafikflöde](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Nedbrytning på grund av topputnyttjande.** Du kan korrelera bandbreddsanvändningstrenden med svarstidstrenden för att identifiera om Azure-arbetsbelastningsförsämringen beror på en topp i bandbreddsanvändning eller inte. Då kan du vidta åtgärder därefter.

![ExpressRoute bandbredd utnyttjande](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Nästa steg
[Sök loggar](../../azure-monitor/log-query/log-query-overview.md) för att visa detaljerade dataposter för nätverksprestanda.
