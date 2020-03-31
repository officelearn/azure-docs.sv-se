---
title: Azure Monitor för nätverk (förhandsversion)
description: En snabb översikt för Azure Monitor för nätverk som ger en omfattande vy över hälso- och mått för alla distribuerade nätverksresurser utan konfiguration.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654876"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor för nätverk (förhandsversion)
Azure Monitor for Network ger en omfattande vy över hälso- och mått för alla distribuerade nätverksresurser utan konfiguration. Den avancerade sökfunktionen hjälper till att identifiera resursberoenden, vilket möjliggör scenarier som att identifiera resurser som är värd för din webbplats genom att helt enkelt söka efter värdbaserade webbplatsnamn.

Sidan Översikt över Azure Monitor för **nätverk** är ett enkelt sätt att visualisera inventeringen av nätverksresurserna tillsammans med resurshälsa och aviseringar. Den är uppdelad i fyra viktiga funktionella områden:

- Sök och filtrera
- Resurshälsa och mått
- Aviseringar 
- Beroendevy

![Översiktssida](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Sök och filtrera
Vyn Resurshälsa och aviseringar kan anpassas med filter som **Prenumeration,** **Resursgrupp** och **Resurstyp**. Sökrutan ger möjlighet att söka igenom resursegenskaper.

Sökrutan kan användas för att söka efter resurser och associerade resurser. En offentlig IP är till exempel kopplad till en programgateway. Om du söker efter DNS-namnet för offentliga IPs identifierar du både offentlig IP och den associerade Application Gateway.

![Search](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Resurshälsa och mått
Varje panel representerar en resurstyp, med antalet instanser som distribueras över alla prenumerationer markerade tillsammans med resurshälsostatus. I exemplet nedan finns det 105 ER- och VPN-anslutningar som distribueras, 103 är felfria och 2 är inte tillgängliga.

![Resurshälsa](media/network-insights-overview/resource-health.png)

Om du klickar på de två otillgängliga ER- och VPN-anslutningarna startar du en måttvy. 

![Måttvy](media/network-insights-overview/metric-view.png)

Du kan klicka på varje element i rutnätsvyn. Klicka på hälsoikonen för att omdirigera till resurshälsa för den anslutningen. Klicka på Aviseringar för att omdirigera till aviseringar respektive måttsida för den anslutningen. 

## <a name="alerts"></a>Aviseringar
Rutnätet **Aviseringar** till höger ger en vy över alla aviseringar som genereras för de valda resurserna i alla prenumerationer. Klicka på aviseringsantalet för att navigera till detaljerade aviseringar sida.

## <a name="dependency-view"></a>Beroendevy
**Beroendevyn** hjälper till att visualisera hur resursen är konfigurerad. För närvarande stöds beroendevyn endast för Application Gateway. Beroendevyn kan nås genom att klicka på programgatewayresursnamnet från rutnätsvyn mått.

![Vyn Programgateway](media/network-insights-overview/application-gateway.png)

**Beroendevyn** för Application Gateway ger en förenklad vy över hur frontend-IP-adresser är anslutna till lyssnarna, reglerna och backend-poolen. Anslutningskanterna är färgkodade och innehåller ytterligare information baserat på serverdapoolens hälsotillstånd. Vyn innehåller också en detaljerad vy över mätvärden och mått för Application Gateway för alla relaterade serverdpooler som VMSS- och VM-instanser.

![Beroendevy](media/network-insights-overview/dependency-view.png)

Beroendediagrammet möjliggör enkel navigering till konfigurationsinställningar. Högerklicka på en backend pool för att komma åt andra funktioner. Om serverda poolen till exempel är en virtuell dator kan du direkt komma åt felsöka VM Insights och Network Watcher-anslutning för att identifiera anslutningsproblem.

![Menyn Beroendevy](media/network-insights-overview/dependency-view-menu.png)

Sök- och filterfältet i beroendevyn är ett enkelt sätt att söka igenom diagrammet. Om du till exempel söker efter *AppGWTestRule* i exemplet nedan begränsas den grafiska vyn till alla noder som är anslutna via *AppGWTestRule*. 

![Sök exempel](media/network-insights-overview/search-example.png)

Olika filter ger hjälp att begränsa till en viss sökväg och tillstånd. Välj till exempel bara *Ohälsosamt* från listrutan **Hälsostatus** för att visa alla kanter där tillståndet är *felfritt*.

Klicka på **Detaljerad måttvy** om du vill starta en förkonfigurerad arbetsbok med detaljerade mått för programgatewayen, alla serverdelspoolresurser och frontend-IP-adresser. 

## <a name="next-steps"></a>Nästa steg 

- Läs mer om nätverksövervakning på Vad är [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview).
