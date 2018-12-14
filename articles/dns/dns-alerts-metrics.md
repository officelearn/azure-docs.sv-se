---
title: Azure DNS-mått och aviseringar | Microsoft Docs
description: Läs mer om Azure DNS – mått och aviseringar.
services: dns
documentationcenter: na
author: vhorne
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: victorh
ms.openlocfilehash: 707a23cbd04db9a0420c7776a24eb568a3483b0b
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385864"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS-mått och aviseringar
Azure DNS är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Den här artikeln beskriver mått och aviseringar för Azure DNS-tjänsten.

## <a name="azure-dns-metrics"></a>Azure DNS-mått

Azure DNS tillhandahåller mått för kunder så att de kan övervaka specifika aspekter av deras UNIKA som finns i tjänsten. Med Azure DNS – mått, kan du dessutom konfigurera och ta emot aviseringar baserat på villkor i närheten. Måtten tillhandahålls de [Azure Monitor-tjänsten](../azure-monitor/index.yml). Azure DNS tillhandahåller följande mått via Azure Monitor för dina DNS-zoner:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Du kan också se den [definitionen av de här måtten](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkdnszones) på dokumentationssidan för Azure Monitor.
>[!NOTE]
> De här måtten är för tillfället bara tillgängliga för offentliga DNS-zoner som finns i Azure DNS. Om du har privata zoner finns i Azure DNS kan tillhandahåller de här måtten inte data för dessa zoner. Dessutom stöds för mått och aviseringar funktionen bara i offentlig Azure-molnet. Stöd för nationella moln följer vid ett senare tillfälle. 

Detaljerad nivå för dimensionen för de här måtten är DNS-zon.

### <a name="query-volume"></a>Fråga volym

Den *fråga volym* mått i Azure DNS visar mängden DNS-frågor (query-trafik) som tas emot av Azure DNS för DNS-zonen. Måttenheten är antalet och aggregeringen är summan av alla frågor som tagits emot under en viss tidsperiod. Om du vill visa det här måttet, väljer du mått (förhandsgranskning) explorer upplevelse från fliken övervakning i Azure-portalen. Välj din DNS-zon Resource listrutan, Välj måtten som frågan volymen och välj summan som aggregering. Skärmbilden nedan visar ett exempel.  Mer information om Metrics Explorer upplevelse och diagram, se [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md).

![Fråga volym](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Bild: Azure DNS-fråga volym-mått*

### <a name="record-set-count"></a>Antal för uppsättningen av poster
Den *ange postantal* måttet visar antalet postuppsättningar i Azure DNS för DNS-zonen. Alla postuppsättningar som definierats i din zon räknas. Måttenheten är antalet och aggregeringen är Maximum för alla postuppsättningar. Om du vill visa det här måttet, Välj **mått (förhandsgranskning)** explorer erfarenhet från den **övervakaren** fliken i Azure-portalen. Välj DNS-zonen från den **Resource** listrutan, väljer den **ange postantal** mått och välj sedan **Max** som den **aggregering** . Mer information om Metrics Explorer upplevelse och diagram, se [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md). 

![Antal för uppsättningen av poster](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Bild: Ange antal för DNS-poster för Azure-mått*


### <a name="record-set-capacity-utilization"></a>Kapacitetsutnyttjande för uppsättningen av poster
Den *post ange Kapacitetsanvändning* mått i Azure DNS visar procentuella andelen användning av din Recordset kapacitet för en DNS-zon. Varje DNS-zon i Azure DNS är föremål för en postuppsättning gränsen som definierar det maximala antalet postuppsättningar som tillåts för zonen (se [DNS gränser](dns-zones-records.md#limits)). Därför kan visar det här måttet hur nära du är att du har nått gränsen för postuppsättningen. Om du har 500 postuppsättningar som konfigurerats för DNS-zonen och zonen har standard Recordset gräns på 5000, till exempel visar RecordSetCapacityUtilization måttet värdet på 10% (som erhålls genom avdelande 500 av 5000). Måttenheten är **procent** och **aggregering** typen är **maximala**. Om du vill visa det här måttet, väljer du mått (förhandsgranskning) explorer upplevelse från fliken övervakning i Azure-portalen. Välj din DNS-zon Resource listrutan, Välj posten ange Kapacitetsanvändning mått och välj Max som aggregering. Skärmbilden nedan visar ett exempel. Mer information om Metrics Explorer upplevelse och diagram, se [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md). 

![Antal för uppsättningen av poster](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Bild: Azure ange Kapacitetsanvändning för DNS-post-mått*

## <a name="alerts-in-azure-dns"></a>Aviseringar i Azure DNS
Azure Monitor innehåller möjligheten att aviseringen mot tillgängliga måttvärden. DNS-mått är tillgängliga i den nya upplevelsen för konfiguration av varning. Som beskrivs i detalj i de [dokumentation aviseringar i Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), välja DNS-zon som resursen, välja mått signaltyp och konfigurera aviseringslogiken och andra parametrar som **Period**och **frekvens**. Du kan definiera ytterligare en [åtgärdsgrupp](../azure-monitor/platform/action-groups.md) för när aviseringen villkor är uppfyllt, där aviseringen levereras via de valda åtgärderna. Mer information om hur du konfigurerar aviseringar för Azure Monitor-mått finns i [skapa, visa och hantera aviseringar med hjälp av Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure DNS](dns-overview.md).
