---
title: Loggar i Azure Monitor | Microsoft-dokument
description: Beskriver loggar i Azure Monitor som används för avancerad analys av övervakningsdata.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 0b288bf6d987b9db682c8d1439879cf6b499f213
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457339"
---
# <a name="logs-in-azure-monitor"></a>Loggar i Azure Monitor

> [!NOTE]
> Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer, mått och loggar. I den här artikeln beskrivs loggar. Se [mått i Azure Monitor](data-platform-metrics.md) för en detaljerad beskrivning av mått och [övervakningsdata som samlats in av Azure Monitor](data-platform.md) för en jämförelse av de två.

Loggar i Azure Monitor är särskilt användbara för att utföra komplexa analyser över data från en mängd olika källor. I den här artikeln beskrivs hur loggar är strukturerade i Azure Monitor, vad du kan göra med data och identifierar olika datakällor som lagrar data i Loggar.

> [!NOTE]
> Det är viktigt att skilja mellan Azure Monitor Loggar och källor till loggdata i Azure. Till exempel skrivs prenumerationsnivåhändelser i Azure till en [aktivitetslogg](platform-logs-overview.md) som du kan visa från Azure Monitor-menyn. De flesta resurser skriver operativ information till en [resurslogg](platform-logs-overview.md) som du kan vidarebefordra till olika platser. Azure Monitor Logs är en loggdataplattform som samlar in aktivitetsloggar och resursloggar tillsammans med andra övervakningsdata för att ge djup analys över hela din uppsättning resurser.

## <a name="what-are-azure-monitor-logs"></a>Vad är Azure Monitor Logs?

Loggar i Azure Monitor innehåller olika typer av data som är ordnade i poster med olika uppsättningar egenskaper för varje typ. Loggar kan innehålla numeriska värden som Azure Monitor Metrics men innehåller vanligtvis textdata med detaljerade beskrivningar. De skiljer sig ytterligare från metriska data genom att de varierar i sin struktur och ofta inte samlas in med jämna mellanrum. Telemetri som händelser och spårningar lagras Azure Monitor Loggar utöver prestandadata så att allt kan kombineras för analys.

En vanlig typ av loggpost är en händelse som samlas in sporadiskt. Händelser skapas av ett program eller en tjänst och innehåller vanligtvis tillräckligt med information för att ge fullständig kontext på egen hand. En händelse kan till exempel indikera att en viss resurs har skapats eller ändrats, en ny värd startade som svar på ökad trafik eller ett fel upptäcktes i ett program.

 Eftersom formatet på data kan variera kan program skapa anpassade loggar med hjälp av den struktur de behöver. Måttdata kan även lagras i Loggar för att kombinera dem med andra övervakningsdata för trender och annan dataanalys.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Vad kan du göra med Azure Monitor Logs?
I följande tabell visas olika sätt som du kan använda Loggar i Azure Monitor.


|  |  |
|:---|:---|
| Analysera | Använd [Log Analytics](../log-query/get-started-portal.md) i Azure-portalen för att skriva [loggfrågor](../log-query/log-query-overview.md) och interaktivt analysera loggdata med hjälp av den kraftfulla datautforskarens analysmotorn.<br>Använd [analytics-konsolen Application Insights](../app/analytics.md) i Azure-portalen för att skriva loggfrågor och interaktivt analysera loggdata från Application Insights. |
| Visualisera | Fäst frågeresultat som återges som tabeller eller diagram till en [Azure-instrumentpanel](../../azure-portal/azure-portal-dashboards.md).<br>Skapa en [arbetsbok](../app/usage-workbooks.md) som ska kombineras med flera datauppsättningar i en interaktiv rapport. <br>Exportera resultatet av en fråga till [Power BI](powerbi.md) för att använda olika visualiseringar och dela med användare utanför Azure.<br>Exportera resultatet av en fråga till [Grafana](grafana-plugin.md) för att utnyttja instrumentpanelen och kombinera med andra datakällor.|
| Varning | Konfigurera en [loggaviseringsregel](alerts-log.md) som skickar ett meddelande eller vidtar [automatiska åtgärder](action-groups.md) när resultatet av frågan matchar ett visst resultat.<br>Konfigurera en [måttaviseringsregel](alerts-metric-logs.md) för vissa loggdataloggar som extraherats som mått. |
| Hämta | Resultat av accessloggfrågor från en kommandorad med [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Resultat av loggfrågor från en kommandorad med [PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Resultat av åtkomstloggfrågor från ett anpassat program med [REST API](https://dev.loganalytics.io/). |
| Exportera | Skapa ett arbetsflöde för att hämta loggdata och kopiera dem till en extern plats med Hjälp av [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Hur är data i Azure Monitor Logs strukturerade?
Data som samlas in av Azure Monitor Logs lagras i en [Log Analytics-arbetsyta](../platform/design-logs-deployment.md). Varje arbetsyta innehåller flera tabeller som varje lagringsdata från en viss källa. Alla tabeller har [vissa gemensamma egenskaper,](log-standard-properties.md)men var och en har en unik uppsättning egenskaper beroende på vilken typ av data som lagras. En ny arbetsyta har standarduppsättning tabeller och fler tabeller läggs till av olika övervakningslösningar och andra tjänster som skriver till arbetsytan.

Loggdata från Application Insights använder samma Log Analytics-motor som arbetsytor, men lagras separat för varje övervakat program. Varje program har en standarduppsättning tabeller för att lagra data som programbegäranden, undantag och sidvisningar.

Loggfrågor använder antingen data från en Log Analytics-arbetsyta eller ett Programinsiktsprogram. Du kan använda en [resursövergripande fråga](../log-query/cross-workspace-query.md) för att analysera programdata tillsammans med andra loggdata eller för att skapa frågor, inklusive flera arbetsytor eller program.

![Arbetsytor](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Loggfrågor
Data i Azure Monitor Logs hämtas med hjälp av en [loggfråga](../log-query/log-query-overview.md) som skrivits med [Kusto-frågespråket](../log-query/get-started-queries.md), vilket gör att du snabbt kan hämta, konsolidera och analysera insamlade data. Använd [Log Analytics](../log-query/portals.md) för att skriva och testa loggfrågor i Azure-portalen. Det gör att du kan arbeta med resultat interaktivt eller fästa dem på en instrumentpanel för att visa dem med andra visualiseringar.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Öppna [Logganalys från Application Insights](../app/analytics.md) för att analysera Application Insights-data.

![Programinsiktsanalys](media/data-platform-logs/app-insights-analytics.png)

Du kan också hämta loggdata med hjälp av [Log Analytics API](https://dev.loganalytics.io/documentation/overview) och REST API för Application [Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Källor till Azure Monitor Logs
Azure Monitor kan samla in loggdata från en mängd olika källor både inom Azure och från lokala resurser. I följande tabeller visas de olika datakällor som är tillgängliga från olika resurser som skriver data till Azure Monitor Logs. Var och en har en länk till information om alla nödvändiga konfigurationer.

### <a name="azure-tenant-and-subscription"></a>Azure-klient och prenumeration

| Data | Beskrivning |
|:---|:---|
| Granskningsloggar för Azure Active Directory | Konfigurerad via diagnostikinställningar för varje katalog. Se [Integrera Azure AD-loggar med Azure Monitor-loggar](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Aktivitetsloggar | Lagras separat som standard och kan användas för varningar i nära realtid. Installera Lösning för Analys av aktivitetsloggen för att skriva till Log Analytics-arbetsytan. Se [Samla in och analysera Azure-aktivitetsloggar i Logganalys](activity-log-collect.md). |

### <a name="azure-resources"></a>Azure-resurser

| Data | Beskrivning |
|:---|:---|
| Resursdiagnostik | Konfigurera diagnostikinställningar för att skriva till diagnostikdata, inklusive mått till en Log Analytics-arbetsyta. Se [Strömma Azure-resursloggar till Log Analytics](resource-logs-collect-workspace.md). |
| Övervakningslösningar | Övervakningslösningar skriver data som de samlar in på sin Log Analytics-arbetsyta. Se [Information om datainsamling för hanteringslösningar i Azure](../insights/solutions-inventory.md) för en lista över lösningar. Mer information om hur du installerar och använder lösningar finns [i Övervakningslösningar i Azure Monitor.](../insights/solutions.md) |
| Mått | Skicka plattformsmått för Azure Monitor-resurser till en Log Analytics-arbetsyta för att behålla loggdata under längre perioder och för att utföra komplex analys med andra datatyper med hjälp av [Kusto-frågespråket](/azure/kusto/query/). Se [Strömma Azure-resursloggar till Log Analytics](resource-logs-collect-storage.md). |
| Azure Table Storage | Samla in data från Azure-lagring där vissa Azure-resurser skriver övervakningsdata. Se [Använda Azure blob storage för IIS och Azure tabelllagring för händelser med Log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Virtuella datorer

| Data | Beskrivning |
|:---|:---|
|  Agentdatakällor | Datakällor som samlas in från [Windows-](agent-windows.md) och [Linux-agenter](../learn/quick-collect-linux-computer.md) inkluderar händelser, prestandadata och anpassade loggar. Se [Agent-datakällor i Azure Monitor](data-sources.md) för en lista över datakällor och information om konfiguration. |
| Övervakningslösningar | Övervakningslösningar skriver data som de samlar in från agenter till sin Log Analytics-arbetsyta. Se [Information om datainsamling för hanteringslösningar i Azure](../insights/solutions-inventory.md) för en lista över lösningar. Mer information om hur du installerar och använder lösningar finns [i Övervakningslösningar i Azure Monitor.](../insights/solutions.md) |
| System Center Operations Manager | Anslut Hanteringsgruppen för Operations Manager till Azure Monitor för att samla in händelse- och prestandadata från lokala agenter i loggar. Mer information om den här konfigurationen finns i [Anslut Operations Manager till Log Analytics.](om-agents.md) |


### <a name="applications"></a>Program

| Data | Beskrivning |
|:---|:---|
| Förfrågningar och undantag | Detaljerade data om programbegäranden och undantag finns i _tabellerna för begäranden,_ _sidvisningar_och _undantag._ Anrop till [externa komponenter](../app/asp-net-dependencies.md) finns i _beroendetabellen._ |
| Användning och prestanda | Prestanda för programmet är tillgängligt i _tabellerna begäranden,_ _webbläsareTimings_ och _performanceCounters._ Data för [anpassade mått](../app/api-custom-events-metrics.md#trackevent) finns i tabellen _customMetrics._|
| Spåra data | Resultat från [distribuerad spårning](../app/distributed-tracing.md) lagras i _spårningstabellen._ |
| Tillgänglighetstester | Sammanfattningsdata från [tillgänglighetstester](../app/monitor-web-app-availability.md) lagras i tabellen _tillgänglighetResultat._ Detaljerade data från dessa tester finns i separat lagring och nås från Application Insights i Azure-portalen. |

### <a name="insights"></a>Insikter

| Data | Beskrivning |
|:---|:---|
| Azure Monitor för containrar | Inventerings- och prestandadata som samlas in av [Azure Monitor för behållare](../insights/container-insights-overview.md). Se Information om insamling av [behållardata](../insights/container-insights-log-search.md#container-records) för en lista över tabellerna. |
| Azure Monitor för virtuella datorer | Kart- och prestandadata som samlas in av [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md). Mer information om [hur du frågar efter loggar från Azure Monitor för virtuella datorer.](../insights/vminsights-log-search.md) |

### <a name="custom"></a>Anpassat 

| Data | Beskrivning |
|:---|:---|
| REST-API | Skriv data till en Log Analytics-arbetsyta från valfri REST-klient. Mer information finns [i Skicka loggdata till Azure Monitor med HTTP Data Collector API.](data-collector-api.md)
| Logikapp | Skriv data till en Log Analytics-arbetsyta från ett Logic App-arbetsflöde med åtgärden **Azure Log Analytics Data Collector.** |

### <a name="security"></a>Säkerhet

| Data | Beskrivning |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) lagrar data som samlas in på en Log Analytics-arbetsyta där den kan analyseras med andra loggdata. Mer information om arbetsytekonfiguration finns [i Datainsamling i Azure Security Center.](../../security-center/security-center-enable-data-collection.md) |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) lagrar data från datakällor till en Log Analytics-arbetsyta. Se [Ansluta datakällor](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor-dataplattformen](data-platform.md).
- Lär dig mer om [mått i Azure Monitor](data-platform-metrics.md).
- Lär dig mer om [övervakningsdata som är tillgängliga](data-sources.md) för olika resurser i Azure.
