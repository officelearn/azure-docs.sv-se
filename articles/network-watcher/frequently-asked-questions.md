---
title: Vanliga frågor och svar om Azure Network Watcher | Microsoft-dokument
description: Den här artikeln besvarar vanliga frågor om Azure Network Watcher-tjänsten.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471864"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Vanliga frågor och svar om Azure Network Watcher
[Azure Network Watcher-tjänsten](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) tillhandahåller en uppsättning verktyg för att övervaka, diagnostisera, visa mått och aktivera eller inaktivera loggar för resurser i ett virtuellt Azure-nätverk. Den här artikeln besvarar vanliga frågor om tjänsten.

## <a name="general"></a>Allmänt

### <a name="what-is-network-watcher"></a>Vad är Network Watcher?
Network Watcher är utformad för att övervaka och reparera nätverkshälsan för IaaS-komponenter (Infrastructure-as-a-Service), som omfattar virtuella datorer, virtuella nätverk, programgateways, belastningsutjämnare och andra resurser i ett virtuellt Azure-nätverk. Det är inte en lösning för att övervaka PaaS-infrastruktur (Platform-as-a-Service) eller få webb/mobilanalys.

### <a name="what-tools-does-network-watcher-provide"></a>Vilka verktyg tillhandahåller Network Watcher?
Network Watcher erbjuder tre viktiga uppsättningar av funktioner
* Övervakning
  * [Topologi vyn](https://docs.microsoft.com/azure/network-watcher/view-network-topology) visar resurserna i ditt virtuella nätverk och relationerna mellan dem.
  * [Med Anslutningsövervakaren](https://docs.microsoft.com/azure/network-watcher/connection-monitor) kan du övervaka anslutningen och svarstiden mellan en virtuell dator och en annan nätverksresurs.
  * [Med nätverksprestandaövervakaren](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) kan du övervaka anslutning och svarstider över hybridnätverksarkitekturer, Expressroute-kretsar och slutpunkter för tjänst/program.  
* Diagnostik
  * [MED IP Flow Verify](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) kan du identifiera trafikfiltreringsproblem på vm-nivå.
  * [Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) hjälper dig att verifiera trafikvägar och identifiera routningsproblem.
  * [Med felsök för anslutning](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) kan en engångsanslutning och svarstid kontrolleras mellan en virtuell dator och en annan nätverksresurs.
  * [Med Packet Capture](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) kan du samla in all trafik på en virtuell dator i det virtuella nätverket.
  * [VPN-felsökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) kör flera diagnostikkontroller på dina VPN-gateways och anslutningar för att felsöka problem.
* Loggning
  * [Med NSG-flödesloggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) kan du logga all trafik i dina [nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) bearbetar dina NSG Flow Log-data så att du kan visualisera, fråga, analysera och förstå din nätverkstrafik.


Mer detaljerad information finns på [översiktssidan för Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Hur fungerar Network Watcher-prissättning?
Besök [prissidan](https://azure.microsoft.com/pricing/details/network-watcher/) för Network Watcher-komponenter och deras priser.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Vilka regioner stöds/är Network Watcher i?
Du kan visa den senaste regionala tillgängligheten på [sidan Azure Service-tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Vilka behörigheter behövs för att använda Network Watcher?
Se listan över [RBAC-behörigheter som krävs för att använda Network Watcher](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions). För att distribuera resurser behöver du deltagarbehörigheter till NetworkWatcherRG (se nedan).

### <a name="how-do-i-enable-network-watcher"></a>Hur gör jag för att välja Network Watcher?
Tjänsten Network Watcher [aktiveras automatiskt](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) för varje prenumeration.

### <a name="what-is-the-network-watcher-deployment-model"></a>Vad är distributionsmodellen för Network Watcher?
Den överordnade nätverksbevakningsresursen distribueras med en unik instans i varje region. Namngivningsformat: NetworkWatcher_RegionName. Exempel: NetworkWatcher_centralus är network watcher-resursen för regionen "Centrala USA".

### <a name="what-is-the-networkwatcherrg"></a>Vad är NetworkWatcherRG?
Nätverksbevakarresurser finns i den dolda **NetworkWatcherRG-resursgruppen** som skapas automatiskt. NSG-flödesloggar är till exempel en underordnad resurs för Network Watcher och är aktiverad i NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Varför måste jag installera network watcher-tillägget? 
Network Watcher-tillägget krävs för alla funktioner som behöver generera eller avlyssna trafik från en virtuell dator. 

### <a name="which-features-require-the-network-watcher-extension"></a>Vilka funktioner kräver network watcher-tillägget?
Funktionerna Paketinsamling, anslutningsfelsökning och anslutningsövervakare måste finnas nätverksbevakningstillägget.

### <a name="what-are-resource-limits-on-network-watcher"></a>Vad är resursbegränsningar för Network Watcher?
Se sidan [Servicegränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) för alla gränser.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Varför tillåts bara en instans av Network Watcher per region? 
Network Watcher behöver bara aktiveras en gång för en prenumeration för att dess funktioner ska fungera, detta är inte en tjänst gräns.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Hur hanterar jag network watcher-resursen? 
Network Watcher-resursen representerar backend-tjänsten för Network Watcher och hanteras helt av Azure. Kunderna behöver inte hantera det. Åtgärder som flyttning stöds inte på resursen. [Resursen kan](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal)dock tas bort . 

## <a name="nsg-flow-logs"></a>NSG-flödesloggar

### <a name="what-does-nsg-flow-logs-do"></a>Vad gör NSG Flow Logs?
Azure-nätverksresurser kan kombineras och hanteras via [NSG(Network Security Groups).](https://docs.microsoft.com/azure/virtual-network/security-overview) Med NSG-flödesloggar kan du logga 5-tuppelflödesinformation om all trafik via nsg:erna. Raw-flödesloggarna skrivs till ett Azure Storage-konto där de kan bearbetas, analyseras, efterfrågas eller exporteras efter behov.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Hur använder jag NSG Flow Logs med ett storage-konto bakom en brandvägg?

Om du vill använda ett Lagringskonto bakom en brandvägg måste du ange ett undantag för betrodda Microsoft-tjänster för att komma åt ditt lagringskonto:

* Navigera till lagringskontot genom att skriva lagringskontots namn i den globala sökningen på portalen eller från [sidan Lagringskonton](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* Under avsnittet **INSTÄLLNINGAR** väljer du **Brandväggar och virtuella nätverk**
* I "Tillåt åtkomst från" väljer du **Markerade nätverk**. Markera sedan rutan bredvid **Tillåt betrodda Microsoft-tjänster att komma åt det här lagringskontot under** **Undantag.** 
* Om det redan är valt behövs ingen ändring.  
* Leta reda på ditt mål NSG på [sidan NSG Flow Logs översikt](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) och aktivera NSG Flow Logs med ovanstående lagringskonto valt.

Du kan kontrollera lagringsloggarna efter några minuter och bör se en uppdaterad tidsstämpel eller en ny JSON-fil som skapats.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Hur använder jag NSG-flödesloggar med ett lagringskonto bakom en tjänstslutpunkt?

NSG-flödesloggar är kompatibla med tjänstslutpunkter utan att kräva någon extra konfiguration. Se [självstudien om hur du aktiverar tjänstslutpunkter](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) i det virtuella nätverket.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Vad är skillnaden mellan flödesloggar version 1 & 2?
Flow Logs version 2 introducerar begreppet *Flow State* & lagrar information om överförda byte och paket. [Läs mer](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Efterföljande moment
 - Gå över till vår [dokumentation översiktssida](https://docs.microsoft.com/azure/network-watcher/) för några tutorials för att komma igång med Network Watcher.
