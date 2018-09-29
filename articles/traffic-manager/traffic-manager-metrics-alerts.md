---
title: Mått och aviseringar i Azure Traffic Manager | Microsoft Docs
description: Den här artikeln beskrivs tillgängliga mått för Traffic Manager i Azure.
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
ms.openlocfilehash: fe4cc3c06af6868396f5db6fd88804022f5f9793
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432485"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager-mått och aviseringar

Traffic Manager får du med DNS-baserade belastningsutjämning som innehåller flera routning metoder och alternativ för slutpunktsövervakning. Den här artikeln beskrivs för mått och tillhörande aviseringar som är tillgängliga för kunder. 

## <a name="metrics-available-in-traffic-manager"></a>Mått som är tillgängliga i Traffic Manager 

Traffic Manager får följande mått på basis av per profil som kan användas av kunder att förstå deras användning av Traffic manager och status för slutpunkter som under denna profil.  

### <a name="queries-by-endpoint-returned"></a>Frågor efter slutpunkten returnerade
Använd [mätvärdet](../monitoring-and-diagnostics/monitoring-supported-metrics.md) att visa antalet frågor som behandlats av en Traffic Manager-profil under en angiven period. Du kan också visa samma information med en slutpunkt på Precision som hjälper dig att förstå hur många gånger en slutpunkt som returnerades i frågesvar från Traffic Manager.

Bild 1 visar alla frågesvar som returnerades av Traffic Manager-profilen i exemplet nedan. 

  
![Mått för Traffic Manager – aggregerad vy över alla frågor](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Bild 1: Samla vy med alla frågor*
  
Bild 2 visar samma information, men den delas av slutpunkter. Därför kan du se mängden frågesvar där en viss slutpunkt returnerades.

![Mått för Traffic Manager – delad vy av frågan volym per slutpunkt](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Bild 2: Delad vy med frågan volym visas per slutpunkt returnerade*

## <a name="endpoint-status-by-endpoint"></a>Slutpunktsstatus av slutpunkten
Använd [mätvärdet](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) att förstå hälsostatus för slutpunkter i profilen. Det tar två värden:
 - Använd **1** om slutpunkten är igång.
 - Använd **0** om slutpunkten är nere.

Det här måttet kan antingen anges som ett samlat värde som representerar statusen för alla mått (bild 3) eller den kan delas (se bild 4) om du vill visa status för slutpunkter. När det gäller tidigare om aggregering nivå markeras som **genomsnittlig**, värdet för den här mått är det aritmetiska medelvärdet av status för alla slutpunkter. Till exempel om en profil har två slutpunkter och endast ett är felfri, det här måttet har värdet **0,50** som visas i bild 3. 


![Mått för Traffic Manager – sammansatta vy över statusen för slutpunkten](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Bild 3: Sammansatta vy över slutpunkten status mått – ”genomsnittlig” aggregering som valts*


![Mått för Traffic Manager – dela vy över statusen för slutpunkten](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Bild 4: Dela överblick över mått för slutpunkt-status*

Du kan använda de här måtten via [Azure Monitor-tjänsten](../monitoring-and-diagnostics/monitoring-supported-metrics.md)'s portalen [REST API](https://docs.microsoft.com/rest/api/monitor/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor), och [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights), eller via avsnittet mått i Traffic Manager-portalen.

## <a name="alerts-on-traffic-manager-metrics"></a>Aviseringar i mått för Traffic Manager
Förutom bearbetning och visa mått från Traffic Manager, Azure Monitor ger kunder möjlighet att konfigurera och mottagning av aviseringar som är associerade med de här måtten. Du kan välja vilka villkor som måste uppfyllas i de här måtten för en avisering ska ske, hur ofta dessa villkor måste övervakas och hur aviseringar ska skickas till dig. Mer information finns i [dokumentation aviseringar i Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Monitor-tjänsten](../monitoring-and-diagnostics/monitoring-supported-metrics.md)
- Lär dig hur du [skapa ett nytt diagram med Azure Monitor](../monitoring-and-diagnostics/monitoring-metric-charts.md#how-do-i-create-a-new-chart)
