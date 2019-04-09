---
title: Mått och aviseringar i Azure Traffic Manager
description: Den här artikeln beskrivs tillgängliga mått för Traffic Manager i Azure.
services: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: 1c0c48efc7abf4b810e92b5cd04d1538577c71d3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268294"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager-mått och aviseringar

Traffic Manager får du DNS-baserade Utjämning av nätverksbelastning som innehåller flera metoder för trafikroutning och alternativ för slutpunktsövervakning. Den här artikeln beskrivs för mått och tillhörande aviseringar som är tillgängliga för kunder. 

## <a name="metrics-available-in-traffic-manager"></a>Mått som är tillgängliga i Traffic Manager 

Traffic Manager får följande mått på basis av per profil som kunder kan använda för att förstå deras användning av Traffic manager och status för slutpunkter som under denna profil.  

### <a name="queries-by-endpoint-returned"></a>Frågor efter slutpunkten returnerade
Använd [mätvärdet](../azure-monitor/platform/metrics-supported.md) att visa antalet frågor som en Traffic Manager-profil som bearbetar under en angiven period. Du kan också visa samma information med en slutpunkt på Precision som hjälper dig att förstå hur många gånger en slutpunkt som returnerades i frågesvar från Traffic Manager.

Bild 1 visar alla frågesvar som returnerar Traffic Manager-profilen i följande exempel. 

  
![Sammanställd vy över alla frågor](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Bild 1: Sammanställd vy med alla frågor*
  
Bild 2 visar samma information, men den delas av slutpunkter. Därför kan du se mängden frågesvar där en viss slutpunkt returnerades.

![Mått för Traffic Manager – delad vy av frågan volym per slutpunkt](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Bild 2: Delad vy med frågan volym visas per slutpunkt returnerade*

## <a name="endpoint-status-by-endpoint"></a>Slutpunktsstatus av slutpunkten
Använd [mätvärdet](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) att förstå hälsostatus för slutpunkter i profilen. Det tar två värden:
 - Använd **1** om slutpunkten är igång.
 - Använd **0** om slutpunkten är nere.

Det här måttet kan antingen anges som ett samlat värde som representerar statusen för alla mått (bild 3) eller den kan delas (se bild 4) om du vill visa status för slutpunkter. Om den tidigare versionen, om aggregering nivå markeras som **genomsnittlig**, värdet för det här måttet är det aritmetiska medelvärdet av status för alla slutpunkter. Till exempel om en profil har två slutpunkter och endast ett är felfri, sedan det här måttet har värdet **0,50** som visas i bild 3. 


![Mått för Traffic Manager – sammansatta vy över statusen för slutpunkten](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Bild 3: Sammansatt vy över slutpunkten status mått – ”genomsnittlig” aggregering som valts*


![Mått för Traffic Manager – dela vy över statusen för slutpunkten](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Bild 4: Dela upp överblick över mått för slutpunkt-status*

Du kan använda de här måtten via [Azure Monitor-tjänsten](../azure-monitor/platform/metrics-supported.md)'s portalen [REST API](https://docs.microsoft.com/rest/api/monitor/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor), och [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights), eller via avsnittet mått i Traffic Manager-portalen.

## <a name="alerts-on-traffic-manager-metrics"></a>Aviseringar i mått för Traffic Manager
Förutom bearbetning och visa mått från Traffic Manager, Azure Monitor ger kunder möjlighet att konfigurera och mottagning av aviseringar som är associerade med de här måtten. Du kan välja vilka villkor som måste uppfyllas i de här måtten för en avisering ska ske, hur ofta dessa villkor måste övervakas och hur aviseringar ska skickas till dig. Mer information finns i [dokumentation aviseringar i Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Monitor-tjänsten](../azure-monitor/platform/metrics-supported.md)
- Lär dig hur du [skapa ett diagram med Azure Monitor](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
