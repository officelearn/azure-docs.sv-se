---
title: Mått och aviseringar - Azure DNS
description: Med den här utbildningssökvägen kommer du igång med Azure DNS-mått och aviseringar.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 42acbc0d32b3ce5de4befcf112b68f611ad70542
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937445"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS-mått och aviseringar
Azure DNS är en värdtjänst för DNS-domäner som tillhandahåller namnmatchning med Microsoft Azure-infrastrukturen. I den här artikeln beskrivs mått och aviseringar för Azure DNS-tjänsten.

## <a name="azure-dns-metrics"></a>Azure DNS-mått

Azure DNS tillhandahåller mått för kunder så att de kan övervaka specifika aspekter av sina DNS-zoner som finns i tjänsten. Dessutom, med Azure DNS-mått, kan du konfigurera och ta emot aviseringar baserat på villkor av intresse. Måtten tillhandahålls via [Azure Monitor-tjänsten](../azure-monitor/index.yml). Azure DNS tillhandahåller följande mått via Azure Monitor för dina DNS-zoner:

-   FrågaVolym
-   RecordSetCount (RecordSetCount)
-   RecordSetCapacityUtilization

Du kan också se definitionen av dessa mått på [dokumentationssidan](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) för Azure Monitor.
>[!NOTE]
> För närvarande är dessa mått endast tillgängliga för offentliga DNS-zoner som finns i Azure DNS. Om du har privata zoner i Azure DNS kommer dessa mått inte att tillhandahålla data för dessa zoner. Dessutom stöds mått och aviseringsfunktionen endast i Azure Public-molnet. Stöd för suveräna moln kommer att följa vid ett senare tillfälle. 

Det mest detaljerade element som du kan se mått för är en DNS-zon. Du kan för närvarande inte se mått för enskilda resursposter inom en zon.

### <a name="query-volume"></a>Fråga volym

Måttet *Frågevolym* i Azure DNS visar volymen DNS-frågor (frågetrafik) som tas emot av Azure DNS för DNS-zonen. Måttenheten är Antal och aggregeringen är summan av alla frågor som tas emot under en tidsperiod. 

Om du vill visa det här måttet väljer du Upplevelse av mått (förhandsversion) på fliken Övervakare i Azure-portalen. Välj DNS-zonen i listrutan Resurs, välj måttet Frågevolym och välj Summa som aggregering. Nedan visar skärmdump ett exempel.  Mer information om hur du utforskar statistik och diagram finns i [Utforskaren för Azure Monitor Metrics](../azure-monitor/platform/metrics-charts.md).

![Fråga volym](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Bild: Azure DNS-frågevolymmått*

### <a name="record-set-count"></a>Antal postuppsättningar
Måttet *Antal postuppsättningar* visar antalet postmängder i Azure DNS för DNS-zonen. Alla postmängder som definierats i zonen räknas. Måttenheten är Antal och aggregeringen är den maximala av alla postmängder. Om du vill visa det här måttet väljer du **Upplevelse av mått (förhandsversion)** på fliken **Övervakare** i Azure-portalen. Välj DNS-zonen i listrutan **Resurs,** välj måttet **Antal postuppsättningar** och välj sedan **Max** som **aggregering**. Mer information om hur du utforskar statistik och diagram finns i [Utforskaren för Azure Monitor Metrics](../azure-monitor/platform/metrics-charts.md). 

![Antal postuppsättningar](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Bild: Azure DNS-postuppsättningsantalsmått*


### <a name="record-set-capacity-utilization"></a>Kapacitetsutnyttjande för postuppsättning
Måttet *Postuppsättning kapacitetsutnyttjande* i Azure DNS visar procentandelen utnyttjande av din postmängdskapacitet för en DNS-zon. Varje DNS-zon i Azure DNS omfattas av en postmängdsgräns som definierar det maximala antalet postmängder som tillåts för zonen (se [DNS-gränser).](dns-zones-records.md#limits) Därför visar det här måttet hur nära du är att träffa postmängdsgränsen. Om du till exempel har 500 postmängder konfigurerade för DNS-zonen och zonen har standardgränsen för postmängd på 5000, visar metric RecordSetCapacityUtilization värdet 10 % (som erhålls genom att dividera 500 med 5000). Måttenheten är **Procent** och **aggregeringstypen** är **Maximum**. Om du vill visa det här måttet väljer du Upplevelse av mått (förhandsversion) på fliken Övervakare i Azure-portalen. Välj DNS-zonen i listrutan Resurs, välj måttet Datainställda kapacitetsutnyttjande och välj Max som aggregering. Nedan visar skärmdump ett exempel. Mer information om hur du utforskar statistik och diagram finns i [Utforskaren för Azure Monitor Metrics](../azure-monitor/platform/metrics-charts.md). 

![Antal postuppsättningar](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Bild: Azure DNS-postuppsättning kapacitetsutnyttjandemått*

## <a name="alerts-in-azure-dns"></a>Aviseringar i Azure DNS
Azure Monitor ger möjlighet att avisera mot tillgängliga måttvärden. DNS-måtten är tillgängliga i den nya aviseringskonfigurationsupplevelsen. Som beskrivs i detalj i [Azure Monitor-aviseringar dokumentationen](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)kan du välja DNS Zone som resurs, välja måttsignaltyp och konfigurera varningslogiken och andra parametrar som **Period** och **Frekvens**. Du kan ytterligare definiera en [åtgärdsgrupp](../azure-monitor/platform/action-groups.md) för när varningsvillkoret är uppfyllt, varvid aviseringen levereras via de valda åtgärderna. Mer information om hur du konfigurerar aviseringar för Azure Monitor-mått finns i [Skapa, visa och hantera aviseringar med Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure DNS](dns-overview.md).
