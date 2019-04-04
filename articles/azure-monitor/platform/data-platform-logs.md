---
title: Loggar i Azure Monitor | Microsoft Docs
description: Beskriver loggar i Azure Monitor som används för avancerad analys av övervakningsdata.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: a7271aa3faf438b42319f8c2c297c6e39baab92e
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904159"
---
# <a name="logs-in-azure-monitor"></a>Loggar i Azure Monitor

> [!NOTE]
> Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer, mått och loggar. Den här artikeln beskriver loggar. Referera till [mått i Azure Monitor](data-platform-metrics.md) för en detaljerad beskrivning av mått och till [övervakningsdata som samlas in av Azure Monitor](data-platform.md) en jämförelse av två.

Loggarna i Azure Monitor är särskilt användbart för att utföra komplexa analyser över data från olika källor. Den här artikeln beskriver hur loggar är strukturerade i Azure Monitor, kan du göra med data, och identifierar olika datakällor som lagrar data i loggarna.

> [!NOTE]
> Det är viktigt att skilja mellan Azure Monitor-loggar och källor av loggdata i Azure. Till exempel prenumeration på händelser i Azure skrivs till en [aktivitetsloggen](activity-logs-overview.md) som du kan visa från Azure Monitor-menyn. De flesta resurser kommer driftsinformation till en [diagnostiklogg](diagnostic-logs-overview.md) som du kan vidarebefordra till olika platser. Azure Monitor-loggar är en log dataplattform som samlar in aktivitetsloggar och diagnostikloggar tillsammans med andra övervakningsdata att tillhandahålla omfattande analyser i dina hela uppsättningen av resurser.

## <a name="what-are-azure-monitor-logs"></a>Vad är Azure Monitor-loggar?

Loggar i Azure Monitor innehåller olika typer av data ordnas i poster med olika uppsättningar med egenskaper för varje typ. Loggar kan innehålla numeriska värden, till exempel Azure Monitor-mått, men vanligtvis innehåller textdata med detaljerade beskrivningar. De ytterligare skiljer sig från måttdata eftersom de kan variera i deras struktur och ofta inte har samlats in med jämna mellanrum. Telemetri, till exempel händelser och spårningar lagras Azure Monitor-loggar förutom prestandadata så att den kan alla kombineras för analys.

En vanlig typ av loggpost är en händelse som har samlats in sporadiskt. Händelser som skapas av programmet eller tjänsten och inkluderar vanligtvis tillräcklig information för att tillhandahålla slutförd kontexten på egen hand. En händelse kan till exempel ange att en viss resurs har skapats eller ändrats, en ny värd igång som svar på ökad trafik, eller ett fel upptäcktes i ett program.

 Eftersom formatet för data kan variera kan program skapa anpassade loggar med hjälp av strukturen som de behöver. Måttdata kan även lagras i loggarna kan kombinera dem med andra övervakningsdata för trender och andra dataanalys.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Vad kan du göra med Azure Monitor-loggar?
I följande tabell visas de olika sätt som du kan använda loggarna i Azure Monitor.


|  |  |
|:---|:---|
| Analysera | Använd [Log Analytics](../log-query/get-started-portal.md) i Azure portal för att skriva [logga frågor](../log-query/log-query-overview.md) och analysera loggdata med hjälp av kraftfulla Data Explorer analysis motorn interaktivt.<br>Använd den [Application Insights analytics-konsolen](../app/analytics.md) i Azure portal för att skriva loggfrågor och analysera loggdata från Application Insights interaktivt. |
| Visualisera | Fästa resultatet av frågan renderas som tabeller eller diagram för en [Azure-instrumentpanelen](../../azure-portal/azure-portal-dashboards.md).<br>Skapa en [arbetsboken](../app/usage-workbooks.md) du kombinerar med flera uppsättningar av data i en interaktiv rapport. <br>Exportera resultatet av en fråga till [Power BI](powerbi.md) att använda olika visualiseringar och dela med användare utanför Azure.<br>Exportera resultatet av en fråga till [Grafana](grafana-plugin.md) att utnyttja dess dashboarding och kombinera med andra datakällor.|
| Varning | Konfigurera en [loggvarningsregler](alerts-log.md) som skickar ett meddelande eller tar [automatisk åtgärd](action-groups.md) när frågans resultat matchar ett visst resultat.<br>Konfigurera en [måttaviseringsregel](alerts-metric-logs.md) på vissa loggar för data som extraheras som mått. |
| Hämta | Åtkomst till loggresultat för frågan från en kommandorad med hjälp av [Azure CLI](/azure/ext/log-analytics/monitor/log-analytics).<br>Åtkomst till loggresultat för frågan från en kommandorad med hjälp av [PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Få åtkomst till loggresultat för frågan från ett anpassat program som använder [REST API](https://dev.loganalytics.io/). |
| Exportera | Skapa ett arbetsflöde för att hämta loggdata och kopiera den till en extern plats med hjälp av [Logikappar](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Vad är data i Azure Monitor-loggar strukturerade?
Data som samlas in av Azure Monitor-loggar lagras i en [Log Analytics-arbetsyta](../platform/manage-access.md). Du kan [skapa flera arbetsytor](manage-access.md#determine-the-number-of-workspaces-you-need) i din prenumeration för att hantera olika uppsättningar av loggdata. Varje arbetsyta innehåller flera tabeller att var och en lagrar data från en viss källa. När alla tabeller dela [vissa gemensamma egenskaper](log-standard-properties.md), var och en har en unik uppsättning egenskaper beroende på vilken typ av data som lagras. En ny arbetsyta har standarduppsättningen med tabeller och flera tabeller kommer att läggas till av olika lösningar för övervakning och andra tjänster som skriver till arbetsytan.

Loggdata från Application Insights använder samma motor för Log Analytics som arbetsytor, men den lagras separat för varje övervakade program. Varje program har en standarduppsättning med tabeller som ska innehålla data, till exempel programbegäranden, undantag och sidvyer.

Loggfrågor att antingen användningsdata från en Log Analytics-arbetsyta eller ett program med Application Insights. Du kan använda en [mellan resurser fråga](../log-query/cross-workspace-query.md) att analysera programdata tillsammans med andra loggdata eller skapa frågor som bland annat flera arbetsytor eller program.

![Arbetsytor](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Loggfrågor
Data i Azure Monitor-loggar som hämtas med hjälp av en [loggfråga](../log-query/log-query-overview.md) skrivna med den [Kusto-frågespråket](../log-query/get-started-queries.md), där du kan snabbt hämta, konsolidera och analysera insamlade data. Använd [Log Analytics](../log-query/portals.md) att skriva och testa loggfrågor i Azure-portalen. Det kan du arbeta interaktivt med resultat eller fästa dem på en instrumentpanel för att visa dem med andra visualiseringar.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Öppna [Log Analytics från Application Insights](../app/analytics.md) att analysera Application Insights-data.

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

Du kan också hämta loggdata med hjälp av den [Log Analytics API](https://dev.loganalytics.io/documentation/overview) och [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Källor för Azure Monitor-loggar
Azure Monitor kan samla in loggdata från olika källor båda i Azure och lokala resurser. I tabellerna nedan listas de olika datakällorna som är tillgängliga från olika resurser som skriva data till Azure Monitor-loggar. Var och en har en länk till information om eventuella obligatoriska konfigurationer.

### <a name="azure-tenant-and-subscription"></a>Azure-klient och prenumeration

| Data | Beskrivning |
|:---|:---|
| Azure Active Directory med granskningsloggar | Konfigurera diagnostikinställningar för varje katalog. Se [integrera Azure AD-loggar med Azure Monitor-loggar](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Aktivitetsloggar | Separat lagras som standard och kan användas för nära realtidsaviseringar. Installera Activity Log Analytics-lösningen för att skriva till Log Analytics-arbetsyta. Se [samla in och analysera Azure-aktivitetsloggar i Log Analytics](collect-activity-logs.md). |

### <a name="azure-resources"></a>Azure-resurser

| Data | Beskrivning |
|:---|:---|
| Resurs-diagnostik | Konfigurera diagnostikinställningar för att skriva till diagnostiska data, inklusive mått till en Log Analytics-arbetsyta. Se [Stream Azure diagnostikloggar till Log Analytics](diagnostic-logs-stream-log-store.md). |
| Övervakningslösningar | Övervakningslösningar skriva data som de samlar in till deras Log Analytics-arbetsyta. Se [Data samling information om lösningar i Azure](../insights/solutions-inventory.md) för en lista över lösningar. Se [övervakningslösningar i Azure Monitor](../insights/solutions.md) för information om hur du installerar och använder lösningar. |
| Mått | Skicka plattform mått för Azure Monitor-resurser till en Log Analytics-arbetsyta att behålla loggdata under längre perioder och utföra komplexa analyser med andra datatyper med hjälp av den [Kusto-frågespråket](/azure/kusto/query/). Se [Stream Azure diagnostikloggar till Log Analytics](diagnostic-logs-stream-log-store.md). |
| Azure Table Storage | Samla in data från Azure storage där vissa Azure-resurser skriva övervakningsdata. Se [Använd Azure blob storage för IIS och Azure table storage för händelser med Log Analytics](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Virtuella datorer

| Data | Beskrivning |
|:---|:---|
|  Agentdatakällor | Datakällor som samlas in från [Windows](agent-windows.md) och [Linux](../learn/quick-collect-linux-computer.md) agenter är händelser, prestandadata och anpassade loggar. Se [agenten datakällor i Azure Monitor](data-sources.md) en lista över datakällor och information om konfigurationen. |
| Övervakningslösningar | Övervakningslösningar skriva data som de samlar in från agenter till arbetsytan Log Analytics. Se [Data samling information om lösningar i Azure](../insights/solutions-inventory.md) för en lista över lösningar. Se [övervakningslösningar i Azure Monitor](../insights/solutions.md) för information om hur du installerar och använder lösningar. |
| System Center Operations Manager | Ansluta Operations Manager-hanteringsgrupp till Azure Monitor för att samla in händelse-och prestandadata från den lokala agenter till loggarna. Se [ansluta Operations Manager till Log Analytics](om-agents.md) mer information om den här konfigurationen. |


### <a name="applications"></a>Appar

| Data | Beskrivning |
|:---|:---|
| Begäranden och undantag | Detaljerad information om programmet begäranden och undantag finns i den _begäranden_, _pageViews_, och _undantag_ tabeller. Anrop till [externa komponenter](../app/asp-net-dependencies.md) finns i den _beroenden_ tabell. |
| Användning och prestanda | Prestanda för programmet är tillgängligt i den _begäranden_, _browserTimings_ och _performanceCounters_ tabeller. Data för [anpassade mått](../app/api-custom-events-metrics.md#trackevent) finns i den _customMetrics_ tabell.|
| Spårningsdata | Resultat av [distribuerad spårning](/app/distributed-tracing) lagras i den _spårningar_ tabell. |
| Tillgänglighetstester | Sammanfattningsdata från [tillgänglighetstester](/app/monitor-web-app-availability) lagras i den _availabilityResults_ tabell. Detaljerade data från dessa tester finns i separat lagring och nås från Application Insights i Azure-portalen. |

### <a name="insights"></a>Insikter

| Data | Beskrivning |
|:---|:---|
| Azure Monitor för behållare | Inventerings-och prestanda som samlas in av [Azure Monitor för behållare](../insights/container-insights-overview.md). Se [datainsamling behållarinformation](../insights/container-insights-analyze.md#container-data-collection-details) en lista över tabellerna. |
| Azure Monitor för virtuella datorer | Kartan och prestandadata som samlats in av [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md). Se [så fråga loggar från Azure Monitor för virtuella datorer](../insights/vminsights-log-search.md) mer information om denna datafrågan. |

### <a name="custom"></a>Anpassat 

| Data | Beskrivning |
|:---|:---|
| REST-API | Skriva data till Log Analytics-arbetsytan från valfri REST-klient. Se [skicka loggdata till Azure Monitor med HTTP Data Collector API](data-collector-api.md) mer information.
| Logikapp | Skriva data till en Log Analytics-arbetsyta från en Logic App-arbetsflöde med den **datainsamlare för Azure Log Analytics** åtgärd. |

### <a name="security"></a>Säkerhet

| Data | Beskrivning |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) lagrar data som samlas in i en Log Analytics-arbetsyta där de kan analyseras med andra loggdata. Se [insamling av Data i Azure Security Center](../../security-center/security-center-enable-data-collection.md) mer information om konfigurationen för arbetsytan. |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) lagrar data från datakällor i en Log Analytics-arbetsyta. Se [](/sentinel/connect-data-sources.md)  |


## <a name="next-steps"></a>Nästa steg

- Läs mer om den [Azure Monitor dataplattform](data-platform.md).
- Lär dig mer om [mått i Azure Monitor](data-platform-metrics.md).
- Lär dig mer om den [övervakningsdata är tillgängliga](data-sources.md) för olika resurser i Azure.
