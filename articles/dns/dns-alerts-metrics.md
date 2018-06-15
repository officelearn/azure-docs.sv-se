---
title: Azure DNS-mätvärden och aviseringar | Microsoft Docs
description: Lär dig mer om Azure DNS mätvärden och -varningar.
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: kumud
ms.openlocfilehash: 54c4df446ee5c1bf8d29dd6c33b304f39ce8f1b8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31591627"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS-mätvärden och -varningar
Azure DNS är en värd-tjänst för DNS-domäner som tillhandahåller namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Den här artikeln beskriver mått och aviseringar för Azure DNS-tjänsten.

## <a name="azure-dns-metrics"></a>Azure DNS-mått

Azure DNS ger mått för kunder för att kunna övervaka vissa aspekter av sina DNS som finns i tjänsten. Med Azure DNS, du dessutom konfigurera och ta emot aviseringar baserat på villkor av intresse. Mätvärdena som tillhandahålls den [Azure-Monitor-tjänsten](../monitoring-and-diagnostics/index.yml). Azure DNS innehåller följande mätvärden via Azure-Monitor för DNS-zoner:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Du kan också se den [definition av de här måtten](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkdnszones) på sidan för Azure-Monitor-dokumentationen.
>[!NOTE]
> De här måtten är bara tillgängliga för offentliga DNS-zoner finns i Azure DNS för tillfället. Om du har privata zoner i Azure DNS kan tillhandahåller de här måtten inte data för dessa zoner. Dessutom stöds mått och aviseringar funktionen bara i Azure offentliga molnet. Stöd för suveräna moln följer vid ett senare tillfälle. 

Detaljerad nivå i dimensionen för de här måtten är DNS-zon.

### <a name="query-volume"></a>Frågan volym

Den *frågan volym* mått i Azure DNS visar de DNS-frågor (query trafik) som tas emot av Azure DNS för DNS-zonen. Måttenheten är antalet aggregeringen är summan av alla frågor som tagits emot över en tidsperiod. Välj mått (förhandsgranskning) explorer upplevelse om du vill visa det här måttet från fliken övervakning i Azure-portalen. Välj din DNS-zonen resurs listrutan, Välj mått i frågan volymen och välj summan som aggregering. Nedan skärmbild visar ett exempel.  Mer information om Metrics Explorer upplevelse och diagram, se [Azure övervakaren Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md).

![Frågan volym](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Bild: Azure DNS-fråga volym mått*

### <a name="record-set-count"></a>Antal för uppsättningen av poster
Den *ange postantal* måttet visar antalet postuppsättningar i Azure DNS för DNS-zonen. Alla postuppsättningar som definierats i zonen räknas. Måttenheten är antalet och aggregeringen är Maximum för alla postuppsättningar. Om du vill visa det här måttet **mått (förhandsgranskning)** explorer erfarenheter från den **övervakaren** fliken i Azure-portalen. Välj din DNS-zonen från den **resurs** listrutan, Välj den **ange postantal** mått och välj sedan **Max** som den **aggregering** . Mer information om Metrics Explorer upplevelse och diagram, se [Azure övervakaren Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Antal för uppsättningen av poster](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Bild: Azure DNS-posten ange antal mått*


### <a name="record-set-capacity-utilization"></a>Postuppsättningen kapacitetsutnyttjande
Den *post ange kapacitetsutnyttjande* mått i Azure DNS visas procentandelen av användning av din Recordset kapacitet för en DNS-zon. Varje DNS-zonen i Azure DNS regleras Recordset gränsen som definierar det maximala antalet postuppsättningar som tillåts för zonen (se [DNS-gränser](dns-zones-records.md#limits)). Därför kan visar det här måttet hur nära du kommer att träffa Recordset gränsen. Om du har 500 postuppsättningar som konfigurerats för DNS-zonen och zonen har Recordset Standardgränsen 5000, kommer RecordSetCapacityUtilization måttet visar värdet för 10% (som erhålls genom avdelande 500 av 5000). Är måttenheten **procentandel** och **aggregering** typ är **maximala**. Välj mått (förhandsgranskning) explorer upplevelse om du vill visa det här måttet från fliken övervakning i Azure-portalen. Välj din DNS-zonen resurs listrutan, Välj posten ange kapacitetsutnyttjande mått och välj Max som sammanställning. Nedan skärmbild visar ett exempel. Mer information om Metrics Explorer upplevelse och diagram, se [Azure övervakaren Metrics Explorer](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Antal för uppsättningen av poster](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Bild: Azure DNS-post anger kapacitetsutnyttjande mått*

## <a name="alerts-in-azure-dns"></a>Aviseringar i Azure DNS
Azure-Monitor ger möjlighet att aviseringen mot tillgängliga mått värden. DNS-mått är tillgängliga i den nya varningskonfiguration upplevelsen. Som beskrivs i detalj i den [Azure-Monitor aviseringar dokumentationen](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), Välj DNS-zon som resursen, Välj signaltyp av mått och konfigurera aviseringen logik och andra parametrar som **Period**och **frekvens**. Du kan definiera ytterligare en [grupp](../monitoring-and-diagnostics/monitoring-action-groups.md) för när aviseringen villkoret är uppfyllt, innebär att aviseringen ska skickas via de valda åtgärderna. Mer information om hur du konfigurerar aviseringar för mått för Azure-Monitor finns [skapa, visa och hantera aviseringar via Azure-Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure DNS](dns-overview.md).
