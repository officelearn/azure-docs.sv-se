---
title: Mått och aviseringar i Azure Traffic Manager
description: I den här artikeln lär du dig de mått och aviseringar som är tillgängliga för Traffic Manager i Azure.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: rohink
ms.openlocfilehash: 521e6ac605d187c0f95545611a17a86cfda6e1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938591"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Mått och aviseringar i Traffic Manager

Traffic Manager ger dig DNS-baserad belastningsutjämning som innehåller flera routningsmetoder och slutpunktsövervakningsalternativ. I den här artikeln beskrivs de mått och tillhörande aviseringar som är tillgängliga för kunder. 

## <a name="metrics-available-in-traffic-manager"></a>Tillgängliga mått i Traffic Manager 

Traffic Manager tillhandahåller följande mått per profil som kunder kan använda för att förstå deras användning av Traffic Manager och statusen för sina slutpunkter under den profilen.  

### <a name="queries-by-endpoint-returned"></a>Frågor efter slutpunkt returneras
Använd [det här måttet](../azure-monitor/platform/metrics-supported.md) om du vill visa antalet frågor som en Traffic Manager-profil bearbetar under en angiven period. Du kan också visa samma information på en slutpunktsnivågranularitet som hjälper dig att förstå hur många gånger en slutpunkt returnerades i frågesvaren från Traffic Manager.

I följande exempel visar figur 1 alla frågesvar som Traffic Manager-profilen returnerar. 

  
![Samlad vy över alla frågor](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Bild 1: Aggregerad vy med alla frågor*
  
Bild 2 visar samma information, men den delas upp efter slutpunkter. Därför kan du se volymen av frågesvar där en viss slutpunkt returnerades.

![Traffic Manager-mått - delad vy över frågevolymen per slutpunkt](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Bild 2: Delad vy med frågevolymen som visas per returnerad slutpunkt*

## <a name="endpoint-status-by-endpoint"></a>Slutpunktsstatus efter slutpunkt
Använd [det här måttet](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) för att förstå hälsotillståndet för slutpunkterna i profilen. Det tar två värden:
 - använda **1** om slutpunkten är uppe.
 - använda **0** om slutpunkten är nere.

Det här måttet kan visas antingen som ett aggregerat värde som representerar status för alla mått (figur 3) eller så kan det delas upp (se bild 4) för att visa status för specifika slutpunkter. Om den förstnämnda, om aggregeringsnivån väljs som **Medel,** är värdet för det här måttet det aritmetiska medelvärdet av status för alla slutpunkter. Om en profil till exempel har två slutpunkter och bara en är felfri, har det här måttet värdet **0,50** som visas i figur 3. 


![Traffic Manager-mått - sammansatt vy över slutpunktsstatus](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Bild 3: Sammansatt vy över slutpunktsstatusmått – "Genomsnittlig" aggregering vald*


![Traffic Manager-mått - delad vy över slutpunktsstatus](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Bild 4: Dela vy över slutpunktsstatusmått*

Du kan använda dessa mått via [Azure Monitor-tjänstens](../azure-monitor/platform/metrics-supported.md)portal, [REST API,](https://docs.microsoft.com/rest/api/monitor/) [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)och [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)eller via måttavsnittet i Traffic Manager portalupplevelse.

## <a name="alerts-on-traffic-manager-metrics"></a>Aviseringar om Trafikhanterarens mått
Förutom att bearbeta och visa mått från Traffic Manager kan Azure Monitor kunder konfigurera och ta emot aviseringar som är associerade med dessa mått. Du kan välja vilka villkor som måste uppfyllas i dessa mått för att en avisering ska inträffa, hur ofta dessa villkor måste övervakas och hur aviseringarna ska skickas till dig. Mer information finns i [dokumentationen för Azure Monitor-aviseringar](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Monitor-tjänsten](../azure-monitor/platform/metrics-supported.md)
- Lär dig hur du [skapar ett diagram med Azure Monitor](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
