---
title: Mått i Azure Monitor | Microsoft Docs
description: Beskriver mått i Azure Monitor som är lätta övervakning av data som har stöd för scenarier i nästan realtid.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 487f70e4055f16c56092f2f970d2a34238e7febe
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851993"
---
# <a name="metrics-in-azure-monitor"></a>Mått i Azure Monitor

> [!NOTE]
> Azure Monitor-dataplattform baseras på två grundläggande datatyper: Mått och loggar. Den här artikeln beskriver mått. Referera till [loggar i Azure Monitor](data-platform-logs.md) för en detaljerad beskrivning av loggar och till [Azure Monitor data platforn](data-platform.md) en jämförelse av två.


Mått i Azure Monitor är lätt och kan stödja scenarier i nästan realtid vilket gör dem särskilt användbart för varningar och snabb identifiering av problem. Den här artikeln beskriver hur mått är strukturerade, vad du kan göra med dem och identifierar olika datakällor som lagrar data i mått.

## <a name="what-are-metrics"></a>Vad är mått?
Mått är numeriska värden som beskriver någon aspekt av ett system vid en given tidpunkt. Mått samlas in med jämna mellanrum och är användbara för avisering eftersom de kan samlas in ofta och en avisering kan vara drar igång snabbt med relativt enkla logik.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Vad kan du göra med Azure Monitor Metrics?
I följande tabell visas de olika sätt som du kan använda måttdata i Azure Monitor.

|  |  |
|:---|:---|
| Analysera | Använd [måttutforskaren](metrics-charts.md) att analysera insamlade mått i ett diagram och jämföra mått från olika resurser. |
| Visualisera | Fästa ett diagram från måttutforskaren till en [Azure-instrumentpanelen](../learn/tutorial-app-dashboards.md).<br>Skapa en [arbetsboken](../app/usage-workbooks.md) du kombinerar med flera uppsättningar av data i en interaktiv rapport. Exportera resultatet av en fråga till [Grafana](grafana-plugin.md) att utnyttja dess dashboarding och kombinera med andra datakällor. |
| Varning | Konfigurera en [måttaviseringsregel](alerts-metric.md) som skickar ett meddelande eller tar [automatisk åtgärd](action-groups.md) när måttet överskrider ett tröskelvärde. |
| Automatisera |  Använd [Autoskala](autoscale-overview.md) att öka eller minska resurser baserat på ett måttvärde korsa ett tröskelvärde. |
| Exportera | [Dirigera mått till loggar](diagnostic-logs-stream-log-store.md) att analysera data i Azure Monitor Metrics tillsammans med data i Azure Monitor-loggar och för att lagra måttvärden under längre tid än 93 dagar.<br>Stream mått för att en [Event Hub](stream-monitoring-data-event-hubs.md) att dirigera dem till externa system. |
| Hämta | Komma åt mätvärden värden från en kommandorad med hjälp av [PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0)<br>Komma åt måttvärden från anpassade program med hjälp av [REST API](rest-api-walkthrough.md).<br>Komma åt mätvärden värden från en kommandorad med hjälp av [CLI](/azure/monitor/metrics). |
| Arkiv | [Arkivera](..//learn/tutorial-archive-data.md) prestanda eller hälsotillstånd historiken för dina resurser för efterlevnad, granskning eller rapportering offline. |


## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Vad är data i Azure Monitor Metrics strukturerade?
Data som samlas in av Azure Monitor Metrics lagras i en time series-databas som är optimerad för att analysera data med tidsstämpel. Varje uppsättning måttvärden är en tidsserie med följande egenskaper:

* Tiden samlades in värdet
* Resursen värdet är associerad med
* Ett namnområde som fungerar som en kategori för måttet
* Ett Måttnamn
* Själva värdet
* Vissa mått kan ha flera dimensioner som beskrivs i [flerdimensionella mått](#multi-dimensional-metrics). Anpassade mått kan ha upp till 10 dimensioner.

Mått i Azure lagras i 93 dagar. Du kan [skicka plattform mått för Azure Monitor-resurser till en Log Analytics-arbetsyta](diagnostic-logs-stream-log-store.md) för långsiktiga trender.

## <a name="multi-dimensional-metrics"></a>Flerdimensionella mått
En av utmaningarna till måttdata är att det ofta har begränsad information för att ge ett sammanhang för insamlade värdena. Azure Monitor löser detta problem med flerdimensionella mått. Storleken på ett mått är namn / värde-par som innehåller ytterligare data för att beskriva mätvärdet. Till exempel ett mått _tillgängligt diskutrymme_ kan ha en dimension som kallas _enhet_ med värden _C:_, _D:_, vilket gör att visa antingen finns tillräckligt med diskutrymme på alla enheter eller för varje enhet individuellt.

Exemplet nedan visar två datauppsättningar för ett hypotetiskt mått som kallas _Nätverksdataflöde_. Den första datauppsättningen har inga dimensioner. Andra datauppsättningen visas värden med två dimensioner _IP-adress_ och _riktning_:

### <a name="network-throughput"></a>Nätverkets dataflöde

| Tidsstämpel     | Måttvärde |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 kbit/s |
| 8/9/2017 8:15 | 1,141.4 kbit/s |
| 8/9/2017 8:16 | 1,110.2 kbit/s |

Den här nolldimensionella mått kan bara svar grundläggande frågor som ”vad var min dataflöde i nätverket vid en given tidpunkt”?

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Nätverkets genomflöde + två dimensioner (”IP” och ”riktning”)

| Tidsstämpel     | Dimensionen ”IP”   | Dimensionen ”riktning” | Måttvärde|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = ”192.168.5.2” | Riktning = ”skicka”    | 646.5 kbit/s |
| 8/9/2017 8:14 | IP = ”192.168.5.2” | Riktning = ”ta emot” | 420.1 kbit/s |
| 8/9/2017 8:14 | IP = ”10.24.2.15”  | Riktning = ”skicka”    | 150.0 kbit/s |
| 8/9/2017 8:14 | IP = ”10.24.2.15”  | Riktning = ”ta emot” | 115,2 kbit/s |
| 8/9/2017 8:15 | IP = ”192.168.5.2” | Riktning = ”skicka”    | 515.2 kbit/s |
| 8/9/2017 8:15 | IP = ”192.168.5.2” | Riktning = ”ta emot” | 371.1 kbit/s |
| 8/9/2017 8:15 | IP = ”10.24.2.15”  | Riktning = ”skicka”    | 155.0 kbit/s |
| 8/9/2017 8:15 | IP = ”10.24.2.15”  | Riktning = ”ta emot” | 100.1 kbit/s |

Det här måttet kan besvara frågor som ”vad var nätverksgenomflöde för varje IP-adress”? och ”hur mycket data som har skickats och tagits emot”? Flerdimensionella mått utföra ytterligare analys och diagnostik värde som jämförs med nolldimensionella mått.

## <a name="interacting-with-azure-monitor-metrics"></a>Interagera med Azure Monitor-mått
Använd [Metrics Explorer](metrics-charts.md) att interaktivt analysera data i databasen mått och diagram värdena för flera mått med tiden. Du kan fästa diagram och en instrumentpanel för att visa dem med andra visualiseringar. Du kan också hämta mått med hjälp av den [Azure monitoring REST API](rest-api-walkthrough.md).

![Metrics Explorer](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Källor för Azure Monitor-mått
Det finns tre grundläggande källor till mätvärden som samlats in från Azure Monitor. När de här måtten har samlats in i Azure Monitor-mått databasen, kan de utvärderas tillsammans oavsett källan.

**Plattformen mått** skapas av Azure-resurser och ger dig insyn i deras hälsotillstånd och prestanda. Varje typ av resurs skapar en [distinkt uppsättning med mått](metrics-supported.md) utan konfiguration krävs. Plattformen mätvärden samlas in från Azure-resurser med en minut frekvens om inget annat anges i definitionen för det mått. 

**Gäst-OS mått** har samlats in från gästoperativsystemet för en virtuell dator. Aktivera mätvärden för gäst-OS för Windows-datorer med [Windows diagnostik tillägget SÄKERHETSSPECIFIKA](../platform/diagnostics-extension-overview.md) och för Linux-datorer med [InfluxData Telegraf agenten](https://www.influxdata.com/time-series-platform/telegraf/).

**Mätvärden för Application** skapas av Application Insights för övervakade program och hjälper dig att identifiera problem med prestanda och spåra trender i hur ditt program används. Detta inkluderar sådana värden som _serversvarstid_ och _webbläsarundantag_.

**Anpassade mått** är mått som du definierar förutom standardmått som är automatiskt tillgängliga. Du kan [definiera anpassade mått i ditt program](../app/api-custom-events-metrics.md) som övervakas av Application Insights eller skapa anpassade mått för ett Azure-tjänsten med hjälp av den [anpassade mått-API](metrics-store-custom-rest-api.md).


## <a name="next-steps"></a>Nästa steg

- Läs mer om den [Azure Monitor dataplattform](data-platform.md).
- Lär dig mer om [logga data i Azure Monitor](data-platform-logs.md).
- Lär dig mer om den [övervakningsdata är tillgängliga](data-sources.md) för olika resurser i Azure.
