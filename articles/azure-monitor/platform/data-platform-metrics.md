---
title: Mått i Azure Monitor | Microsoft Docs
description: Beskriver mått i Azure Monitor som är lätta att övervaka data som stöder nästan real tids scenarier.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: e534754e46e6f2ad9b99b67d24d9f7da63a51a4f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258365"
---
# <a name="metrics-in-azure-monitor"></a>Mått i Azure Monitor

> [!NOTE]
> Azure Monitor data plattform baseras på två grundläggande data typer: Mått och loggar. I den här artikeln beskrivs mått. [I loggarna i Azure Monitor](data-platform-logs.md) hittar du en detaljerad beskrivning av loggar och [Azure Monitor data plattform](data-platform.md) för en jämförelse av de två.

Mått i Azure Monitor är lätta och kan stödja scenarier i nära real tid som gör det särskilt användbart för aviseringar och snabb identifiering av problem. Den här artikeln beskriver hur mått är strukturerade, vad du kan göra med dem och identifierar olika data källor som lagrar data i mått.

## <a name="what-are-metrics"></a>Vad är mått?
Mått är numeriska värden som beskriver någon aspekt av ett system vid en given tidpunkt. Mått samlas in med jämna mellanrum och är användbara för aviseringar eftersom de kan samplas ofta och en avisering kan utlösas snabbt med relativt enkel logik.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Vad kan du göra med Azure Monitor mått?
I följande tabell visas de olika sätt som du kan använda Metric-data i Azure Monitor.

|  |  |
|:---|:---|
| Analysera | Använd [Metrics Explorer](metrics-charts.md) för att analysera insamlade mått i ett diagram och jämföra mått från olika resurser. |
| Visualisera | Fäst ett diagram från metrics Explorer till en [Azure-instrumentpanel](../learn/tutorial-app-dashboards.md).<br>Skapa en [arbets bok](../app/usage-workbooks.md) som ska kombineras med flera data uppsättningar i en interaktiv rapport. Exportera resultatet av en fråga till [Grafana](grafana-plugin.md) för att dra nytta av dess instrument panel och kombinera med andra data källor. |
| Varning | Konfigurera en [regel för mått varningar](alerts-metric.md) som skickar ett meddelande eller [automatiserar en åtgärd](action-groups.md) när mått värdet korsar ett tröskelvärde. |
| Automatisera |  Använd [autoskalning](autoscale-overview.md) för att öka eller minska resurser baserat på ett mått värde som korsar ett tröskelvärde. |
| Exportera | [Dirigera mått till loggar](resource-logs-collect-storage.md) för att analysera data i Azure Monitor mått tillsammans med data i Azure Monitor loggar och lagra mått värden i mer än 93 dagar.<br>Strömma mått till en [Event Hub](stream-monitoring-data-event-hubs.md) för att dirigera dem till externa system. |
| Hämta | Komma åt Mät värden från en kommando rad med [PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Använd [REST API](rest-api-walkthrough.md)för att komma åt Mät värden från anpassade program.<br>Komma åt Mät värden från en kommando rad med [CLI](/cli/azure/monitor/metrics). |
| Arkiv | [Arkivera](..//learn/tutorial-archive-data.md) prestanda eller hälsotillstånd historiken för dina resurser för efterlevnad, granskning eller rapportering offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Hur struktureras data i Azure Monitor mått?
Data som samlas in av Azure Monitor mått lagras i en databas för tids serier som är optimerade för analys av tidsstämplade data. Varje uppsättning metriska värden är en tids serie med följande egenskaper:

* Tiden då värdet samlades in
* Resursen som värdet är associerat med
* Ett namn område som fungerar som en kategori för måttet
* Ett mått namn
* Själva värdet
* Vissa mått kan ha flera dimensioner enligt beskrivningen i [flerdimensionella mått](#multi-dimensional-metrics). Anpassade mått kan ha upp till 10 dimensioner.

## <a name="multi-dimensional-metrics"></a>Flerdimensionella mått
En av utmaningarna med mät data är att den ofta har begränsad information för att tillhandahålla kontext för insamlade värden. Azure Monitor åtgärdar denna utmaning med flerdimensionella mått. Storleken på ett mått är namn / värde-par som innehåller ytterligare data för att beskriva mätvärdet. Till exempel kan ett mått på måttet som _är tillgängligt_ ha en dimension som heter _enhet_ med värdena _C:_ , _D:_ , vilket kan visa antingen tillgängligt disk utrymme på alla enheter eller för varje enhet individuellt.

Exemplet nedan visar två datauppsättningar för ett hypotetiskt mått som kallas _Nätverksdataflöde_. Den första datauppsättningen har inga dimensioner. Den andra data mängden visar värdena med två dimensioner, _IP-adress_ och _riktning_:

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

## <a name="interacting-with-azure-monitor-metrics"></a>Interagera med Azure Monitor mått
Använd [Metrics Explorer](metrics-charts.md) för att interaktivt analysera data i din mått databas och rita upp värdena för flera mått över tid. Du kan fästa diagrammen på en instrument panel för att visa dem med andra visualiseringar. Du kan också hämta mått med hjälp av den [Azure monitoring REST API](rest-api-walkthrough.md).

![Metrics Explorer](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Källor för Azure Monitor mått
Det finns tre grundläggande källor till mätvärden som samlats in från Azure Monitor. När dessa mått samlas in i Azure Monitor Metric-databasen kan de utvärderas tillsammans oavsett källa.

**Plattformen mått** skapas av Azure-resurser och ger dig insyn i deras hälsotillstånd och prestanda. Varje typ av resurs skapar en [distinkt uppsättning med mått](metrics-supported.md) utan konfiguration krävs. Plattforms mått samlas in från Azure-resurser med en minuters frekvens om inget annat anges i måttets definition. 

**Gäst operativ** systemets mått samlas in från gäst operativ systemet på en virtuell dator. Aktivera gäst operativ systemets mått för virtuella Windows-datorer med [Windows Diagnostic Extension (wad)](../platform/diagnostics-extension-overview.md) och för virtuella Linux-datorer med InfluxData f- [agenten](https://www.influxdata.com/time-series-platform/telegraf/).

**Mätvärden för Application** skapas av Application Insights för övervakade program och hjälper dig att identifiera problem med prestanda och spåra trender i hur ditt program används. Detta inkluderar sådana värden som _serversvarstid_ och _webbläsarundantag_.

**Anpassade mått** är mått som du definierar förutom de standard mått som är tillgängliga automatiskt. Du kan [definiera anpassade mått i ditt program](../app/api-custom-events-metrics.md) som övervakas av Application Insights eller skapa anpassade mått för en Azure-tjänst med hjälp av [anpassade Metrics-API: et](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Kvarhållning av mått
För de flesta resurser i Azure lagras måtten i 93 dagar. Det finns vissa undantag:

**Gäst operativ systemets mått**
-   **Klassiska gäst operativ systemets mått**. Detta är prestanda räknare som samlas in av [Windows Diagnostic-tillägget (wad)](../platform/diagnostics-extension-overview.md) eller [Linux Diagnostic Extension (lad)](../../virtual-machines/extensions/diagnostics-linux.md) och dirigeras till ett Azure Storage-konto. Kvarhållning för dessa mått är 14 dagar.
-   **Gäst operativ systemets mått skickas till Azure Monitor mått**. Detta är prestanda räknare som samlas in av Windows Diagnostic-tillägget (WAD) och skickas till [Azure Monitor-mottagare](diagnostics-extension-overview.md#data-storage)eller via [InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) -på Linux-datorer. Kvarhållning för dessa mått är 93 dagar.
-   **Gäst operativ system mått som samlats in av Log Analytics-agenten**. Detta är prestanda räknare som samlas in av Log Analytics agent och skickas till en Log Analytics arbets yta. Kvarhållning för dessa mått är 31 dagar och kan utökas upp till två år.

**Application Insights log-baserade mått**. 
- Efter scenen översätts [loggbaserade mått](../app/pre-aggregated-metrics-log-metrics.md) till logg frågor. Deras kvarhållning matchar kvarhållning av händelser i underliggande loggar. För Application Insights resurser lagras loggar i 90 dagar.


> [!NOTE]
> Du kan [Skicka plattforms mått för Azure Monitor resurser till en Log Analytics arbets yta](resource-logs-collect-storage.md) för långsiktiga trender.





## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor data plattform](data-platform.md).
- Lär dig mer om [loggdata i Azure Monitor](data-platform-logs.md).
- Lär dig mer om den [övervakningsdata är tillgängliga](data-sources.md) för olika resurser i Azure.
