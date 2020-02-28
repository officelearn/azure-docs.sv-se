---
title: Azure Monitor för nätverk (för hands version)
description: En snabb översikt för Azure Monitor för nätverk som ger en omfattande vy över hälsa och mått för alla distribuerade nätverks resurser utan någon konfiguration.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654876"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor för nätverk (för hands version)
Azure Monitor för nätverk ger en omfattande vy över hälsa och mått för alla distribuerade nätverks resurser utan någon konfiguration. Den avancerade Sök funktionen hjälper till att identifiera resurs beroenden, vilket möjliggör scenarier som att identifiera resurser som är värdar för din webbplats genom att helt enkelt söka efter namnet på den värdbaserade webbplatsen.

**Översikts** sidan för Azure Monitor för nätverk är ett smidigt sätt att visualisera inventeringen av dina nätverks resurser tillsammans med resurs hälsa och aviseringar. Den är uppdelad i fyra viktiga funktions områden:

- Sökning och filtrering
- Resource Health och mått
- Aviseringar 
- Beroende vy

![Översikts sida](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Sökning och filtrering
Vyn resurs hälsa och aviseringar kan anpassas med hjälp av filter som **prenumeration**, **resurs grupp** och **resurs typ**. Sökrutan ger möjlighet att söka igenom resurs egenskaper.

Du kan använda sökrutan för att söka efter resurser och associerade resurser. Till exempel är en offentlig IP-adress kopplad till en Application Gateway. Om du söker efter offentliga IP DNS-namn identifieras både offentlig IP-adress och tillhör ande Application Gateway.

![Söka](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Resource Health och mått
Varje panel representerar en resurs typ med antalet instanser som distribueras över alla prenumerationer som valts tillsammans med resursens hälso status. I exemplet nedan finns det 105 ER-och VPN-anslutningar som har distribuerats, 103 är felfria och 2 otillgängliga.

![Resurshälsa](media/network-insights-overview/resource-health.png)

Om du klickar på de två otillgängliga återställnings-och VPN-anslutningarna öppnas en Metric-vy. 

![Metric-vy](media/network-insights-overview/metric-view.png)

Du kan klicka på varje-element i diagramvyn. Klicka på hälso ikonen för att omdirigera till resurs hälsa för den anslutningen. Klicka på aviseringar för att omdirigera till aviseringar och mått sidan för anslutningen. 

## <a name="alerts"></a>Aviseringar
I rutnätet **aviseringar** till höger får du en översikt över alla aviseringar som har genererats för de valda resurserna i alla prenumerationer. Klicka på sidan antal aviseringar för att gå till sidan detaljerade aviseringar.

## <a name="dependency-view"></a>Beroende vy
**Beroende** vyn hjälper till att visualisera hur resursen konfigureras. För hands beroende vy stöds bara för Application Gateway. Du kan komma åt beroende vyn genom att klicka på Application Gateway resurs namn från vyn mått rutnät.

![Application Gateway vy](media/network-insights-overview/application-gateway.png)

**Beroende** vyn för Application Gateway ger en förenklad vy över hur klient delens IP-adresser är anslutna till lyssnare, regler och backend-pool. De anslutande kanterna är färgkodade och ger ytterligare information baserat på Server delens hälso tillstånd. Vyn innehåller också en detaljerad vy över Application Gateway mått och mått för alla relaterade Server dels pooler, till exempel VMSS och VM-instanser.

![Beroende vy](media/network-insights-overview/dependency-view.png)

Med det beroende diagrammet kan du enkelt navigera till konfigurations inställningar. Högerklicka på en backend-pool för att få åtkomst till andra funktioner. Om till exempel backend-poolen är en virtuell dator kan du direkt komma åt VM-insikter och Network Watcher anslutnings fel sökning för att identifiera anslutnings problem.

![Meny för beroende vy](media/network-insights-overview/dependency-view-menu.png)

Sök-och filter fältet i beroende vyn ger ett smidigt sätt att söka igenom grafen. Om du till exempel söker efter *AppGWTestRule* i exemplet nedan så begränsas den grafiska vyn till alla noder som är anslutna via *AppGWTestRule*. 

![Sök exempel](media/network-insights-overview/search-example.png)

Olika filter ger hjälp att begränsa till en specifik sökväg och tillstånd. Välj till exempel endast *ohälsosamt* från List rutan **hälso status** för att visa alla kanter där tillståndet är *dåligt*.

Klicka på **vyn detaljerad statistik** för att starta en förkonfigurerad arbets bok med detaljerade mått för programgatewayen, alla resurser för Server dels poolen och klient dels-IP-adresser. 

## <a name="next-steps"></a>Nästa steg 

- Läs mer om nätverks övervakning i vad är [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview).
