---
title: Mått i Azure Monitor | Microsoft-dokument
description: Beskriver mått i Azure Monitor som är lätta övervakningsdata som kan stödja scenarier i nära realtid.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a02b3df02d455db8a7dfd21f63d659f75a66e446
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457322"
---
# <a name="metrics-in-azure-monitor"></a>Mått i Azure Monitor

> [!NOTE]
> Azure Monitor-dataplattformen baseras på två grundläggande datatyper: Mått och loggar. I den här artikeln beskrivs mått. Se [Loggar i Azure Monitor](data-platform-logs.md) för en detaljerad beskrivning av loggar och [Azure Monitor-dataplattform](data-platform.md) för en jämförelse av de två.

Mått i Azure Monitor är lätta och kan stödja scenarier i nära realtid, vilket gör dem särskilt användbara för aviseringar och snabb identifiering av problem. I den här artikeln beskrivs hur mått är strukturerade, vad du kan göra med dem och identifierar olika datakällor som lagrar data i mått.

## <a name="what-are-metrics"></a>Vad är mått?
Mått är numeriska värden som beskriver någon aspekt av ett system vid en viss tidpunkt. Mått samlas in med jämna mellanrum och är användbara för aviseringar eftersom de kan samplas ofta, och en avisering kan avfyras snabbt med relativt enkel logik.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Vad kan du göra med Azure Monitor Metrics?
I följande tabell visas olika sätt som du kan använda måttdata i Azure Monitor.

|  |  |
|:---|:---|
| Analysera | Använd [statistikutforskaren](metrics-charts.md) för att analysera insamlade mått i ett diagram och jämföra mått från olika resurser. |
| Visualisera | Fästa ett diagram från statistikutforskaren till en [Azure-instrumentpanel](../learn/tutorial-app-dashboards.md).<br>Skapa en [arbetsbok](../app/usage-workbooks.md) som ska kombineras med flera datauppsättningar i en interaktiv rapport. Exportera resultatet av en fråga till [Grafana](grafana-plugin.md) för att utnyttja instrumentpanelen och kombinera med andra datakällor. |
| Varning | Konfigurera en [måttaviseringsregel](alerts-metric.md) som skickar ett meddelande eller vidtar [automatiska åtgärder](action-groups.md) när måttvärdet överskrider ett tröskelvärde. |
| Automatisera |  Använd [Automatisk skalning](autoscale-overview.md) för att öka eller minska resurser baserat på ett måttvärde som överskrider ett tröskelvärde. |
| Exportera | [Dirigera mått till loggar](resource-logs-collect-storage.md) för att analysera data i Azure Monitor-mått tillsammans med data i Azure Monitor-loggar och för att lagra måttvärden längre än 93 dagar.<br>Strömma mått till en [händelsehubb](stream-monitoring-data-event-hubs.md) för att dirigera dem till externa system. |
| Hämta | Komma åt måttvärden från en kommandorad med [PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Få åtkomst till måttvärden från anpassat program med [REST API](rest-api-walkthrough.md).<br>Få tillgång till måttvärden från en kommandorad med [CLI](/cli/azure/monitor/metrics). |
| Arkiv | [Arkivera](..//learn/tutorial-archive-data.md) prestanda- eller hälsohistoriken för din resurs för efterlevnad, granskning eller offlinerapportering. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Hur är data i Azure Monitor Metrics strukturerade?
Data som samlas in av Azure Monitor Metrics lagras i en tidsseriedatabas som är optimerad för att analysera tidsstämplade data. Varje uppsättning måttvärden är en tidsserie med följande egenskaper:

* Den tid då värdet samlades in
* Resursen som värdet är associerat med
* Ett namnområde som fungerar som en kategori för måttet
* Ett måttnamn
* Själva värdet
* Vissa mått kan ha flera dimensioner enligt beskrivningen i [flerdimensionella mått](#multi-dimensional-metrics). Anpassade mått kan ha upp till 10 dimensioner.

## <a name="multi-dimensional-metrics"></a>Flerdimensionella mätvärden
En av utmaningarna för metriska data är att den ofta har begränsad information för att tillhandahålla kontext för insamlade värden. Azure Monitor tar itu med den här utmaningen med flerdimensionella mått. Dimensioner för ett mått är namnvärdespar som innehåller ytterligare data för att beskriva måttvärdet. Ett mått _Tillgängligt diskutrymme_ kan till exempel ha en dimension som kallas _Enhet_ med värden _C:_, _D:_, vilket gör det möjligt att visa antingen tillgängligt diskutrymme på alla enheter eller för varje enhet individuellt.

Exemplet nedan illustrerar två datauppsättningar för ett hypotetiskt mått som kallas _Nätverksdataflöde_. Den första datauppsättningen har inga dimensioner. Den andra datauppsättningen visar värdena med två dimensioner, _IP-adress_ och _riktning:_

### <a name="network-throughput"></a>Nätverksdataflöde

| Tidsstämpel     | Måttvärde |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 kbps |
| 8/9/2017 8:15 | 1 141,4 kbit/s |
| 8/9/2017 8:16 | 1 110,2 kbit/s |

Detta icke-dimensionella mått kan bara svara på en grundläggande fråga som "vad var mitt nätverksdataflöde vid en viss tidpunkt?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Nätverksgenomströmning + två dimensioner ("IP" och "Riktning")

| Tidsstämpel     | Dimension "IP"   | Dimension "Riktning" | Måttvärde|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Riktning="Skicka"    | 646,5 kbit/s |
| 8/9/2017 8:14 | IP="192.168.5.2" | Riktning="Ta emot" | 420,1 kbit/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Riktning="Skicka"    | 150,0 kbit/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Riktning="Ta emot" | 115,2 kbit/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Riktning="Skicka"    | 515,2 kbit/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Riktning="Ta emot" | 371,1 kbit/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Riktning="Skicka"    | 155,0 kbit/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Riktning="Ta emot" | 100,1 kbit/s |

Det här måttet kan svara på frågor som "vad var nätverksflödet för varje IP-adress?", och "hur mycket data skickades kontra togs emot?" Flerdimensionella mått har ytterligare analytiskt och diagnostiskt värde jämfört med icke-dimensionella mått.

## <a name="interacting-with-azure-monitor-metrics"></a>Interagera med Azure Monitor-mått
Använd [Metrics Explorer](metrics-charts.md) för att interaktivt analysera data i måttdatabasen och kartlägga värdena för flera mått över tid. Du kan fästa diagrammen på en instrumentpanel för att visa dem med andra visualiseringar. Du kan också hämta mått med hjälp av [AZURE-övervaknings-REST API](rest-api-walkthrough.md).

![Metrics Explorer](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Källor till Azure Monitor-mått
Det finns tre grundläggande källor till mått som samlas in av Azure Monitor. När dessa mått har samlats in i Azure Monitor-måttdatabasen kan de utvärderas tillsammans oavsett källa.

**Plattformsmått skapas** av Azure-resurser och ger dig insyn i deras hälsa och prestanda. Varje typ av resurs skapar en [distinkt uppsättning mått](metrics-supported.md) utan någon konfiguration som krävs. Plattformsmått samlas in från Azure-resurser med en minuts frekvens om inget annat anges i måttets definition. 

**Gäst-OS-mått** samlas in från gästoperativsystemet för en virtuell dator. Aktivera gäst-OS-mått för virtuella Windows-datorer med [Windows Diagnostic Extension (WAD)](../platform/diagnostics-extension-overview.md) och för virtuella Linux-datorer med [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/).

**Programmått skapas** av Application Insights för dina övervakade program och hjälper dig att identifiera prestandaproblem och spåra trender i hur ditt program används. Detta inkluderar värden som _Serversvarstid_ och _webbläsarundantag_.

**Anpassade mått** är mått som du definierar utöver de standardmått som är automatiskt tillgängliga. Du kan [definiera anpassade mått i ditt program](../app/api-custom-events-metrics.md) som övervakas av Application Insights eller skapa anpassade mått för en Azure-tjänst med hjälp av [API:et för anpassade mått](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Lagring av mått
För de flesta resurser i Azure lagras mått i 93 dagar. Det finns några undantag:

**Mätvärden för gästoperativsystem**
-   **Klassiska gäst-OS-mätvärden**. Det här är prestandaräknare som samlas in av [Windows Diagnostic Extension (WAD)](../platform/diagnostics-extension-overview.md) eller [Linux Diagnostic Extension (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) och dirigeras till ett Azure-lagringskonto. Kvarhållning för dessa mått är 14 dagar.
-   **Gäst-OS-mått som skickas till Azure Monitor Metrics**. Det här är prestandaräknare som samlas in av [Windows Diagnostic Extension (WAD)](diagnostics-extension-overview.md) och skickas till [Azure Monitor-datamottagaren](diagnostics-extension-overview.md#data-destinations)eller via [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/) på Linux-datorer. Kvarhållning för dessa mått är 93 dagar.
-   **Gäst-OS-mått som samlats in av Log Analytics-agent**. Det här är prestandaräknare som samlas in av Log Analytics-agenten och skickas till en Log Analytics-arbetsyta. Kvarhållning för dessa mått är 31 dagar och kan förlängas upp till 2 år.

**Programinsikter loggbaserade mått**. 
- Bakom scenen översätter [loggbaserade mått](../app/pre-aggregated-metrics-log-metrics.md) till loggfrågor. Deras kvarhållning matchar bevarandet av händelser i underliggande loggar. För Application Insights-resurser lagras loggar i 90 dagar.


> [!NOTE]
> Du kan [skicka plattformsmått för Azure Monitor-resurser till en Log Analytics-arbetsyta](resource-logs-collect-storage.md) för långsiktig trendning.





## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor-dataplattformen](data-platform.md).
- Lär dig mer om [loggdata i Azure Monitor](data-platform-logs.md).
- Lär dig mer om [övervakningsdata som är tillgängliga](data-sources.md) för olika resurser i Azure.
