---
title: Introduktion till Azure Nätverksbevakaren | Microsoft Docs
description: Den här sidan innehåller en översikt över tjänsten Nätverksbevakaren för övervakning och visualisering av nätverk anslutet resurser i Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 792b96e4f5ba5dc0f2f943f099a2fee339407d66
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="azure-network-monitoring-overview"></a>Azure-nätverk övervakning-översikt

Kunder kan du skapa ett nätverk i Azure genom att samordna och skapa olika enskilda nätverksresurser, till exempel virtuella nätverk, ExpressRoute, Programgateway, belastningsutjämnare och flera. Övervakning är tillgänglig på alla nätverksresurser. Vi refererar till den här övervakning som nivån Resursövervakning.

Slutpunkt till slutpunkt-nätverket kan ha komplexa konfigurationer och samverkan mellan resurser, skapa komplicerade scenarier som behöver scenariobaserade övervakning via Nätverksbevakaren.

Den här artikeln beskriver scenariot och nivå Resursövervakning. Nätverksövervakning i Azure är omfattande och omfattar två kategorier:

* [**Nätverk Watcher** ](#network-watcher) -scenariot-baserad övervakning tillhandahålls med funktionerna i Nätverksbevakaren. Den här tjänsten innehåller paketinsamling, nästa hopp, IP-flöde Kontrollera NSG flödet loggar i gruppvyn säkerhet. Scenariot nivån övervakning innehåller en heltäckande vy av nätverksresurser i stället för enskilda resurs nätverksövervakning.
* [**Resursövervakning** ](#network-resource-level-monitoring) -nivå Resursövervakning består av fyra funktioner, diagnostiska loggar, mått, felsökning och resurshälsa. Alla dessa funktioner är byggda på resursen nätverksnivån.

## <a name="network-watcher"></a>Network Watcher

Nätverksbevakaren är en regionala tjänst som gör att du kan övervaka och diagnostisera villkor på nätverket scenariot nivå, till och från Azure. Diagnostiska nätverks- och visualiseringsverktyg som finns tillgängliga med Nätverksbevakaren hjälpa dig att förstå, diagnostisera och få insyn i nätverket i Azure.

Nätverksbevakaren har för närvarande följande funktioner:

* **[Topologi](network-watcher-topology-overview.md)**  -nätverket nivån visa visar de olika anslutningarna och associationer mellan nätverksresurser i en resursgrupp.
* **[Variabeln paketinsamling](network-watcher-packet-capture-overview.md)**  -samlar in paketdata till och från en virtuell dator. Avancerade alternativ för filtrering och finjustera kontroller, till exempel att kunna ange tid och storlek begränsningar ger flexibilitet. Paketdata kan lagras i en blobstore eller på den lokala disken i CAP-format.
* **[Kontrollera IP-flöde](network-watcher-ip-flow-verify-overview.md)**  -kontrollerar om ett paket tillåts eller nekas baserat på flödet information 5-tuppel paket parametrar (mål-IP, käll-IP, målport, källport och Protocol). Om paketet nekas av en säkerhetsgrupp, returneras regeln och grupp som nekas paketet.
* **[Nästa hopp](network-watcher-next-hop-overview.md)**  -avgör nästa hopp för paket som vidarebefordras i Azure-nätverksinfrastruktur, vilket gör det lättare att diagnostisera eventuella felkonfigurerat användardefinierade vägar.
* **[Säkerhet gruppvyn](network-watcher-security-group-view-overview.md)**  -hämtar effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.
* **[NSG flöda loggning](network-watcher-nsg-flow-logging-overview.md)**  -flöde Nätverkssäkerhetsgrupper loggarna gör det möjligt att samla in loggar som rör trafik som tillåts eller nekas av säkerhetsregler i gruppen. Flödet definieras av en 5-tuppel information – käll-IP, mål-IP, källport, mål Port och protokoll.
* **[Virtuella Nätverksgatewayen och anslutningen felsökning](network-watcher-troubleshoot-manage-rest.md)**  -ger dig möjlighet att felsöka virtuella Nätverksgatewayer och anslutningar.
* **[Nätverk prenumerationsbegränsningar](#network-subscription-limits)**  -kan du visa nätverksresursanvändning mot gränser.
* **[Konfigurera diagnostik loggen](#diagnostic-logs)**  – innehåller en om du vill aktivera eller inaktivera diagnostik loggar för nätverksresurser i en resursgrupp.
* **[Felsöka anslutning](network-watcher-connectivity-overview.md)**  -verifierar möjligheten att upprätta en direkt TCP-anslutning från en virtuell dator till en viss slutpunkt utökat med Azure kontext.
* **[Övervakaren anslutning](connection-monitor.md)**  -övervaka problem svarstid och konfiguration mellan en virtuell Azure-dator och en IP-adress med hjälp av käll- och IP-adress och port.

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Rollbaserad åtkomstkontroll (RBAC) i Nätverksbevakaren

Watcher nätverket i [rollbaserad åtkomstkontroll (RBAC) modellen](../active-directory/role-based-access-control-what-is.md). Följande behörigheter krävs av Nätverksbevakaren. Det är viktigt att se till att den roll som används för att initiera nätverket Watcher API: er eller använda Nätverksbevakaren från portalen har tillräcklig behörighet.

|Resurs| Behörigheter|
|---|---| 
|Microsoft.Storage/ |Läs|
|Microsoft.Authorization/| Läs| 
|Microsoft.Resources/subscriptions/resourceGroups/| Läs|
|Microsoft.Storage/storageAccounts/listServiceSas/ | åtgärd|
|Microsoft.Storage/storageAccounts/listAccountSas/ |åtgärd|
|Microsoft.Storage/storageAccounts/listKeys/ | åtgärd|
|Microsoft.Compute/virtualMachines/ |Läs|
|Microsoft.Compute/virtualMachines/ |Skriv|
|Microsoft.Compute/virtualMachineScaleSets/ |Läs|
|Microsoft.Compute/virtualMachineScaleSets/ |Skriv|
|Microsoft.Network/networkWatchers/packetCaptures/ |Läs|
|Microsoft.Network/networkWatchers/packetCaptures/| Skriv|
|Microsoft.Network/networkWatchers/packetCaptures/| Radera|
|Microsoft.Network/networkWatchers/ |Skriv |
|Microsoft.Network/networkWatchers/| Läs |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>Nätverket prenumerationsbegränsningar

Nätverket prenumerationsbegränsningar ger dig information om användning av varje nätverksresurs i en prenumeration i en region mot det maximala antalet tillgängliga resurser.

![prenumerationsgränsen för nätverk][nsl]

## <a name="network-resource-level-monitoring"></a>Resursen nivån nätverksövervakning

Följande funktioner är tillgängliga för nivån Resursövervakning:

### <a name="audit-log"></a>granskningslogg

Åtgärder som utförs som en del av konfigurationen av nätverk loggas. Dessa loggar kan visas i Azure-portalen eller hämtas med hjälp av Microsoft-verktyg, till exempel Power BI eller verktyg från tredje part. Granskningsloggar är tillgängliga via portalen, PowerShell, CLI och Rest-API. Mer information om granskningsloggarna finns [granskningsåtgärder med Resource Manager](../resource-group-audit.md)

Granskningsloggar är tillgängliga för åtgärder som utförs på alla nätverksresurser.

### <a name="metrics"></a>Mått

Mått är prestandamått och prestandaräknare som samlats in under en viss tidsperiod. Mått är tillgängliga för Programgateway. Mått kan användas för att utlösa varningar baserat på tröskelvärdet. Se [Gateway Programdiagnostik](../application-gateway/application-gateway-diagnostics.md) att visa hur mått kan användas för att skapa aviseringar.

![Visa mått][metrics]

### <a name="diagnostic-logs"></a>Diagnostikloggar

Periodiska och eget initiativ händelser skapas av nätverksresurser och inloggad storage-konton, skickas till en Händelsehubb eller logganalys. Dessa loggar ger insikter om hälsotillståndet för en resurs. Dessa loggar kan visas i verktyg som Power BI och logganalys. Ta reda på hur du visar diagnostikloggar [logganalys](../log-analytics/log-analytics-azure-networking-analytics.md).

Diagnostikloggar är tillgängliga för [belastningsutjämnaren](../load-balancer/load-balancer-monitor-log.md), [Nätverkssäkerhetsgrupper](../virtual-network/virtual-network-nsg-manage-log.md), vägar och [Programgateway](../application-gateway/application-gateway-diagnostics.md).

Nätverksbevakaren ger diagnostikloggar vyn. Den här vyn innehåller alla nätverksresurser som stöder diagnostikloggning. I den här vyn kan du aktivera och inaktivera nätverksresurser lätt och snabbt.

![loggar][logs]

### <a name="troubleshooting"></a>Felsökning

Bladet felsökning en upplevelse i portalen finns på nätverksresurser idag att felsöka vanliga problem som är associerade med en enskild resurs. Det här upplevelsen är tillgängligt för följande nätverksresurser - ExpressRoute, VPN-Gateway, Programgateway, säkerhetsloggar i nätverket, vägar, DNS, belastningsutjämnare och Traffic Manager. Mer information om resurs nivån felsökning finns [diagnostisera och lösa problem med Azure-felsökning](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)

![felsökningsinformation][TS]

### <a name="resource-health"></a>Resurshälsa

Hälsotillståndet för en nätverksresurs tillhandahålls på regelbunden basis. Dessa resurser inkluderar VPN-Gateway och VPN-tunnel. Resurshälsotillståndet är tillgänglig på Azure-portalen. Läs mer om resurshälsa [Resource Health översikt](../resource-health/resource-health-overview.md)

## <a name="next-steps"></a>Nästa steg

När du lära dig mer om Nätverksbevakaren dig du att:

Gör en paketinsamling på den virtuella datorn genom att besöka [variabeln paketinsamling i Azure-portalen](network-watcher-packet-capture-manage-portal.md)

Utföra Proaktiv övervakning och diagnostik använder [aviseringen utlöses paketinsamling](network-watcher-alert-triggered-packet-capture.md).

Identifiera säkerhetsproblem med [analysera paketinsamling med Wireshark](network-watcher-deep-packet-inspection.md), med hjälp av verktyg för öppen källkod.

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png











