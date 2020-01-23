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
ms.openlocfilehash: 856c249b72e9e0ff8667d10821ad14b3432b0775
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509197"
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

### <a name="what-are-resource-limits-on-network-watcher"></a>Vad är resurs gränser på Network Watcher?
Se sidan [tjänst begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) för alla gränser.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Varför är bara en instans av Network Watcher tillåtet per region?
Network Watcher behöver bara aktive ras en gång för att en prenumeration på IT-funktioner ska fungera, är detta inte en tjänst gräns.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Varför måste jag installera Network Watcher-tillägget? 
Network Watcher-tillägget krävs för alla funktioner som behöver generera eller avlyssna trafik från en virtuell dator. 

### <a name="which-features-require-the-network-watcher-extension"></a>Vilka funktioner kräver Network Watcher-tillägget?
Endast paket fångst, anslutnings fel sökning och anslutnings övervakare behöver Network Watcher-tillägget för att finnas.

## <a name="nsg-flow-logs"></a>NSG flödes loggar

### <a name="what-does-nsg-flow-logs-do"></a>Vad gör NSG Flow-loggarna?
Azure nätverks resurser kan kombineras och hanteras via [nätverks säkerhets grupper (NSG: er)](https://docs.microsoft.com/azure/virtual-network/security-overview). Med NSG Flow-loggar kan du logga 5 tuple-flödes information om all trafik via din NSG: er. Obehandlade flödes loggar skrivs till ett Azure Storage-konto där de kan bearbetas, analyseras, efter frågas eller exporteras efter behov.

### <a name="how-do-i-use-nsg-flow-logs-on-a-storage-account-with-a-firewall"></a>Hur gör jag för att använda flödes loggar för NSG på ett lagrings konto med en brand vägg?

Om du vill använda ett lagrings konto med en brand vägg måste du ange ett undantag för betrodda Microsoft-tjänster för åtkomst till ditt lagrings konto:

* Navigera till lagrings kontot genom att skriva lagrings kontots namn i den globala sökningen på portalen eller från [sidan lagrings konton](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
* Under avsnittet **INSTÄLLNINGAR** väljer du **Brandväggar och virtuella nätverk**
* I "Tillåt åtkomst från" väljer du **valda nätverk**. Under **undantag**, kryssar du i rutan bredvid **"Tillåt betrodda Microsoft-tjänster för att få åtkomst till det här lagrings kontot"** 
* Om det redan är valt behövs ingen ändring.  
* Leta upp mål NSG på [översikts sidan för NSG Flow-loggar](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) och aktivera NSG flödes loggar med ovanstående lagrings konto valt.

Du kan kontrollera lagringsloggarna efter några minuter och bör se en uppdaterad tidsstämpel eller en ny JSON-fil som skapats.

### <a name="how-do-i-use-nsg-flow-logs-with-service-endpoints-for-storage"></a>Hur gör jag för att använda flödes loggar för NSG med tjänst slut punkter för lagring?

Se [självstudien om hur du aktiverar tjänstens slut punkter](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint). 


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Vad är skillnaden mellan flödes loggar version 1 & 2?
Flödes loggar version 2 introducerar konceptet *flödes tillstånd* & lagrar information om byte och paket som överförs. [Läs mer](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Efterföljande moment
 - Gå till vår [översikts sida för dokumentation](https://docs.microsoft.com/azure/network-watcher/) för några självstudier för att komma igång med Network Watcher.
