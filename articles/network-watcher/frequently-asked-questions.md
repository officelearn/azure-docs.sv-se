---
title: Vanliga frågor och svar om Azure Network Watcher | Microsoft Docs
description: I den här artikeln får du svar på vanliga frågor om Azure Network Watcher-tjänsten.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471864"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Vanliga frågor och svar om Azure Network Watcher
[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -tjänsten innehåller en uppsättning verktyg för att övervaka, diagnostisera, Visa mått och aktivera eller inaktivera loggar för resurser i ett virtuellt Azure-nätverk. I den här artikeln besvaras vanliga frågor om tjänsten.

## <a name="general"></a>Allmänt

### <a name="what-is-network-watcher"></a>Vad är Network Watcher?
Network Watcher har utformats för att övervaka och reparera nätverks hälso tillståndet för IaaS-komponenter (Infrastructure-as-a-Service), inklusive Virtual Machines, virtuella nätverk, programgatewayer, belastningsutjämnare och andra resurser i ett virtuellt Azure-nätverk. Det är inte en lösning för övervakning av PaaS-infrastruktur (Platform-as-a-Service) eller hämtning av webb-/mobil analyser.

### <a name="what-tools-does-network-watcher-provide"></a>Vilka verktyg tillhandahåller Network Watcher?
Network Watcher tillhandahåller tre huvudsakliga uppsättningar funktioner
* Övervakning
  * I [vyn topologi](https://docs.microsoft.com/azure/network-watcher/view-network-topology) visas resurserna i det virtuella nätverket och relationerna mellan dem.
  * Med [anslutnings övervakaren](https://docs.microsoft.com/azure/network-watcher/connection-monitor) kan du övervaka anslutningar och svars tider mellan en virtuell dator och en annan nätverks resurs.
  * Med [övervakaren för nätverks prestanda](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) kan du övervaka anslutningar och fördröjningar i hybrid nätverks arkitekturer, ExpressRoute-kretsar och slut punkter för tjänst/program.  
* Diagnostik
  * Med [verifiera IP-flöde](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) kan du identifiera trafik filtrerings problem på VM-nivå.
  * Med [nästa hopp](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) kan du verifiera trafik vägar och identifiera problem med routningen.
  * [Anslutnings fel sökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) möjliggör en eng ång slönas anslutning och en svars tids kontroll mellan en virtuell dator och en annan nätverks resurs.
  * Med [paket fångst](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) kan du samla all trafik på en virtuell dator i det virtuella nätverket.
  * [VPN-felsökning](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) kör flera diagnostiska kontroller på dina VPN-gatewayer och anslutningar för att hjälpa till att felsöka problem.
* Loggning
  * Med [NSG Flow-loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) kan du logga all trafik i dina [nätverks säkerhets grupper (NSG: er)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Trafikanalys](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) bearbetar NSG Flow-loggdata så att du kan visualisera, fråga, analysera och förstå nätverks trafiken.


Mer detaljerad information finns på sidan om [Network Watcher översikt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Hur fungerar Network Watcher prissättning?
Besök [sidan med priser](https://azure.microsoft.com/pricing/details/network-watcher/) för Network Watcher komponenter och deras priser.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Vilka regioner Network Watcher stöds/är tillgängliga i?
Du kan visa den senaste regionala tillgängligheten på [sidan tillgänglighet för Azure-tjänsten](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Vilka behörigheter krävs för att använda Network Watcher?
Se listan över [RBAC-behörigheter som krävs för att använda Network Watcher](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions). För att distribuera resurser måste du ha deltagar behörighet till NetworkWatcherRG (se nedan).

### <a name="how-do-i-enable-network-watcher"></a>Hur gör jag för att välja Network Watcher?
Tjänsten Network Watcher [aktive ras automatiskt](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) för varje prenumeration.

### <a name="what-is-the-network-watcher-deployment-model"></a>Vad är Network Watcher distributions modell?
Den Network Watcher överordnade resursen distribueras med en unik instans i varje region. Namngivnings format: NetworkWatcher_RegionName. Exempel: NetworkWatcher_centralus är Network Watcher resursen för regionen "Central US".

### <a name="what-is-the-networkwatcherrg"></a>Vad är NetworkWatcherRG?
Network Watcher-resurser finns i den dolda resurs gruppen **NetworkWatcherRG** som skapas automatiskt. Till exempel är resursen NSG Flow-loggar en underordnad resurs till Network Watcher och aktive ras i NetworkWatcherRG.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Varför måste jag installera Network Watcher-tillägget? 
Network Watcher-tillägget krävs för alla funktioner som behöver generera eller avlyssna trafik från en virtuell dator. 

### <a name="which-features-require-the-network-watcher-extension"></a>Vilka funktioner kräver Network Watcher-tillägget?
Insamlings funktionerna för paket insamling, anslutning och anslutning för anslutning kräver att Network Watcher-tillägget är tillgängligt.

### <a name="what-are-resource-limits-on-network-watcher"></a>Vad är resurs gränser på Network Watcher?
Se sidan [tjänst begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) för alla gränser.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Varför är bara en instans av Network Watcher tillåtet per region? 
Network Watcher behöver bara aktive ras en gång för att en prenumeration på IT-funktioner ska fungera, är detta inte en tjänst gräns.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Hur kan jag hantera Network Watchers resursen? 
Network Watcher resursen representerar backend-tjänsten för Network Watcher och hanteras helt av Azure. Kunderna behöver inte hantera den. Åtgärder som flytta stöds inte på resursen. [Det går dock att ta bort resursen](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal). 

## <a name="nsg-flow-logs"></a>NSG flödes loggar

### <a name="what-does-nsg-flow-logs-do"></a>Vad gör NSG Flow-loggarna?
Azure nätverks resurser kan kombineras och hanteras via [nätverks säkerhets grupper (NSG: er)](https://docs.microsoft.com/azure/virtual-network/security-overview). Med NSG Flow-loggar kan du logga 5 tuple-flödes information om all trafik via din NSG: er. Obehandlade flödes loggar skrivs till ett Azure Storage-konto där de kan bearbetas, analyseras, efter frågas eller exporteras efter behov.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Hur gör jag för att använda flödes loggar för NSG med ett lagrings konto bakom en brand vägg?

Om du vill använda ett lagrings konto bakom en brand vägg måste du ange ett undantag för betrodda Microsoft-tjänster för åtkomst till ditt lagrings konto:

* Navigera till lagrings kontot genom att skriva lagrings kontots namn i den globala sökningen på portalen eller från [sidan lagrings konton](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* Under avsnittet **INSTÄLLNINGAR** väljer du **Brandväggar och virtuella nätverk**
* I "Tillåt åtkomst från" väljer du **valda nätverk**. Under **undantag**, kryssar du i rutan bredvid **"Tillåt betrodda Microsoft-tjänster för att få åtkomst till det här lagrings kontot"** 
* Om det redan är valt behövs ingen ändring.  
* Leta upp mål NSG på [översikts sidan för NSG Flow-loggar](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) och aktivera NSG flödes loggar med ovanstående lagrings konto valt.

Du kan kontrollera lagringsloggarna efter några minuter och bör se en uppdaterad tidsstämpel eller en ny JSON-fil som skapats.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Hur gör jag för att använda flödes loggar för NSG med ett lagrings konto bakom en tjänst slut punkt?

NSG flödes loggar är kompatibla med tjänst slut punkter utan att kräva någon extra konfiguration. Se [självstudien om hur du aktiverar tjänstens slut punkter](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) i det virtuella nätverket.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Vad är skillnaden mellan flödes loggar version 1 & 2?
Flödes loggar version 2 introducerar konceptet *flödes tillstånd* & lagrar information om byte och paket som överförs. [Läs mer](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Nästa steg
 - Gå till vår [översikts sida för dokumentation](https://docs.microsoft.com/azure/network-watcher/) för några självstudier för att komma igång med Network Watcher.
