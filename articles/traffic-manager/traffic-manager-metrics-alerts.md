---
title: Mått och aviseringar i Azure Traffic Manager | Microsoft Docs
description: Den här artikeln beskriver tillgängliga mått för Traffic Manager i Azure.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: 424782be2d814df6d598591198b5005fb494d3da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304206"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager-mätvärden och -varningar

Traffic Manager ger DNS-baserade belastningsbalanseringen som innehåller flera routning metoder och slutpunkten alternativ för övervakning. Den här artikeln beskriver mått och associerade aviseringar som är tillgängliga för kunder. 

## <a name="metrics-available-in-traffic-manager"></a>Mått som är tillgängliga i Traffic Manager 

Traffic Manager ger följande mått på grundval av per profil som kan användas av kunder att förstå deras användning av Traffic manager och status för sina slutpunkter under denna profil.  

### <a name="queries-by-endpoint-returned"></a>Frågor av slutpunkten som returnerade
Använd [mätvärdet](../monitoring-and-diagnostics/monitoring-supported-metrics.md) att visa antalet frågor som behandlats av en Traffic Manager-profil under en angiven period. Du kan också visa samma information på en slutpunkt nivån granularitet som hjälper till att du förstår hur många gånger en slutpunkt returnerades i frågesvar från Traffic Manager.

I exemplet nedan visar bild 1 alla frågesvar som returnerades av trafikhanterarprofilen. 

  
![Mätvärden för Traffic Manager - aggregerad vy över alla frågor](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Bild 1: Samla vy med alla frågor*
  
Bild 2 visar samma information, men den delas med slutpunkter. Därför kan du se datavolym som svar på frågor som en viss slutpunkts returnerades.

![Mätvärden för Traffic Manager - delad vy av frågan volym per slutpunkt](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Bild 2: Delad vy med frågan volymen visas per slutpunkt returnerade*

## <a name="endpoint-status-by-endpoint"></a>Status för endpoint av slutpunkten
Använd [mätvärdet](../monitoring-and-diagnostics/monitoring-supported-metrics.md) att förstå hälsostatus för slutpunkter i profilen. Det tar två värden:
 - Använd **1** om slutpunkten är igång.
 - Använd **0** om slutpunkten är igång.

Det här måttet kan antingen anges som ett samlat värde som representerar statusen för alla mått (bild 3) eller den kan delas (se figur 4) om du vill visa status för specifika slutpunkter. När det gäller tidigare, om nivån aggregeringen är markerad som **Avg**, värdet för den här mått är det aritmetiska medelvärdet av status för alla slutpunkter. Till exempel om en profil har två slutpunkter och endast ett är felfri, med det här måttet har värdet **0,50** som visas i bild 3. 


![Mätvärden för Traffic Manager - sammansatta vy av status för endpoint](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Bild 3: Sammansatta vy av slutpunkten status mått – ”medel” aggregering valt*


![Mätvärden för Traffic Manager - delad vy av status för endpoint](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Bild 4: Delad vy av slutpunkten status metrics*

Du kan använda dessa mätvärden via [Azure-Monitor-tjänsten](../monitoring-and-diagnostics/monitoring-supported-metrics.md)'s portalen [REST API](https://docs.microsoft.com/rest/api/monitor/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor), och [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights), eller via avsnittet mått i Traffic Manager-Portaler.

## <a name="alerts-on-traffic-manager-metrics"></a>Aviseringar om Traffic Manager-mått
Förutom bearbetning och visa mått från Traffic Manager, Övervakare för Azure ger kunder möjlighet att konfigurera och ta emot aviseringar som är associerade med de här måtten. Du kan välja vilka villkor måste vara uppfyllda i de här måtten för en avisering ska ske, hur ofta dessa villkor behöver övervakas och hur aviseringar ska skickas till dig. Mer information finns i [Azure-Monitor aviseringar dokumentationen](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure-Monitor-tjänsten](../monitoring-and-diagnostics/monitoring-supported-metrics.md)
- Lär dig hur du [skapar ett nytt diagram med hjälp av Azure-Monitor](../monitoring-and-diagnostics/monitoring-metric-charts.md#how-do-i-create-a-new-chart)