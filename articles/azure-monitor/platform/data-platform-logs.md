---
title: Loggar i Azure Monitor | Microsoft Docs
description: Beskriver loggar i Azure Monitor som används för avancerad analys av övervaknings data.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 06b35fdcd9da333c3c9d94d41c84a2f25ede59ad
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666573"
---
# <a name="logs-in-azure-monitor"></a>Loggar i Azure Monitor

> [!NOTE]
> Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer, mått och loggar. I den här artikeln beskrivs loggar. Se [mått i Azure Monitor](data-platform-metrics.md) för en detaljerad beskrivning av mått och [övervakning av data som samlas in av Azure Monitor](data-platform.md) för en jämförelse mellan de två.

Loggar i Azure Monitor är särskilt användbara för att utföra komplexa analyser mellan data från olika källor. Den här artikeln beskriver hur loggar struktureras i Azure Monitor, vad du kan göra med data och identifierar olika data källor som lagrar data i loggar.

> [!NOTE]
> Det är viktigt att skilja mellan Azure Monitor loggar och källor till loggdata i Azure. Händelser på prenumerations nivå i Azure skrivs till exempel till en [aktivitets logg](platform-logs-overview.md) som du kan visa från Azure Monitor-menyn. De flesta resurser kommer att skriva funktions information till en [resurs logg](platform-logs-overview.md) som du kan vidarebefordra till olika platser. Azure Monitor loggar är en logg data plattform som samlar in aktivitets loggar och resurs loggar tillsammans med andra övervaknings data för att ge en djup analys över hela uppsättningen med resurser.

## <a name="what-are-azure-monitor-logs"></a>Vad är Azure Monitor loggar?

Loggar i Azure Monitor innehåller olika typer av data som är ordnade i poster med olika uppsättningar egenskaper för varje typ. Loggar kan innehålla numeriska värden som Azure Monitor mått, men vanligt vis innehåller text data med detaljerade beskrivningar. De skiljer sig ytterligare från Mät data som de skiljer sig åt i deras struktur och samlas ofta inte in med jämna mellanrum. Telemetri som händelser och spår lagras Azure Monitor loggar förutom prestanda data så att alla kan kombineras för analys.

En vanlig typ av loggpost är en händelse som samlas in sporadiskt. Händelser skapas av ett program eller en tjänst och innehåller vanligt vis tillräckligt med information för att tillhandahålla fullständig kontext på egen hand. En händelse kan till exempel ange att en viss resurs har skapats eller ändrats, en ny värd igång som svar på ökad trafik, eller ett fel upptäcktes i ett program.

 Eftersom formatet för data kan variera kan program skapa anpassade loggar med hjälp av strukturen som de behöver. Mät data kan även lagras i loggar för att kombinera dem med andra övervaknings data för utveckling och annan data analys.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Vad kan du göra med Azure Monitor loggar?
I följande tabell visas de olika sätt som du kan använda för att logga in Azure Monitor.


|  |  |
|:---|:---|
| Analysera | Använd [Log Analytics](../log-query/get-started-portal.md) i Azure Portal för att skriva [logg frågor](../log-query/log-query-overview.md) och analysera logg data interaktivt med hjälp av den kraftfulla datautforskaren analys motorn.<br>Använd [Application Insights Analytics-konsolen](../app/analytics.md) i Azure Portal om du vill skriva logg frågor och analysera loggdata interaktivt från Application Insights. |
| Visualisera | Fäst frågeresultaten som återges som tabeller eller diagram på en [Azure-instrumentpanel](../../azure-portal/azure-portal-dashboards.md).<br>Skapa en [arbets bok](../app/usage-workbooks.md) som ska kombineras med flera data uppsättningar i en interaktiv rapport. <br>Exportera resultatet av en fråga för att [Power BI](powerbi.md) att använda olika visualiseringar och dela med användare utanför Azure.<br>Exportera resultatet av en fråga till [Grafana](grafana-plugin.md) för att dra nytta av dess instrument panel och kombinera med andra data källor.|
| Varning | Konfigurera en [logg varnings regel](alerts-log.md) som skickar ett meddelande eller [automatiserar en åtgärd](action-groups.md) när resultatet av frågan matchar ett visst resultat.<br>Konfigurera en [mått varnings regel](alerts-metric-logs.md) för vissa logg data loggar som extraheras som mått. |
| Hämta | Få åtkomst till logg frågeresultat från en kommando rad med hjälp av [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Kom åt logg frågeresultaten från en kommando rad med [PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Få åtkomst till logg frågeresultaten från ett anpassat program med hjälp av [REST API](https://dev.loganalytics.io/). |
| Exportera | Bygg ett arbets flöde för att hämta loggdata och kopiera det till en extern plats med hjälp av [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Hur är data i Azure Monitor-loggar strukturerade?
Data som samlas in av Azure Monitor loggar lagras i en [Log Analytics arbets yta](../platform/design-logs-deployment.md). Varje arbets yta innehåller flera tabeller som lagrar data från en viss källa. Alla tabeller har samma [gemensamma egenskaper](log-standard-properties.md), men varje har en unik uppsättning egenskaper beroende på vilken typ av data som lagras. En ny arbets yta kommer att ha en standard uppsättning tabeller och fler tabeller läggs till av olika övervaknings lösningar och andra tjänster som skriver till arbets ytan.

Loggdata från Application Insights använder samma Log Analytics motor som arbets ytor, men lagras separat för varje övervakat program. Varje program har en standard uppsättning tabeller för att lagra data, till exempel program begär Anden, undantag och sid visningar.

Logg frågor använder antingen data från en Log Analytics arbets yta eller ett Application Insights program. Du kan använda en [kors resurs fråga](../log-query/cross-workspace-query.md) för att analysera program data tillsammans med andra loggdata eller för att skapa frågor, till exempel flera arbets ytor eller program.

![Arbetsytor](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Loggfrågor
Data i Azure Monitor loggar hämtas med hjälp av en [logg fråga](../log-query/log-query-overview.md) som skrivits med [Kusto-frågespråket](../log-query/get-started-queries.md), vilket gör att du snabbt kan hämta, konsolidera och analysera insamlade data. Använd [Log Analytics](../log-query/portals.md) för att skriva och testa logg frågor i Azure Portal. Det gör att du kan arbeta med resultaten interaktivt eller fästa dem på en instrument panel för att visa dem med andra visualiseringar.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Öppna [Log Analytics från Application Insights](../app/analytics.md) för att analysera Application Insights data.

![Application Insights analys](media/data-platform-logs/app-insights-analytics.png)

Du kan också hämta loggdata med hjälp av [Log Analytics API](https://dev.loganalytics.io/documentation/overview) och [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Källor till Azure Monitor loggar
Azure Monitor kan samla in loggdata från olika källor både i Azure och från lokala resurser. I följande tabeller visas de olika data källor som är tillgängliga från olika resurser som skriver data till Azure Monitor loggar. Var och en har en länk till information om alla konfigurationer som krävs.

### <a name="azure-tenant-and-subscription"></a>Azure-klient och prenumeration

| Data | Beskrivning |
|:---|:---|
| Azure Active Directory gransknings loggar | Konfigureras via diagnostikinställningar för varje katalog. Se [integrera Azure AD-loggar med Azure Monitor loggar](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Aktivitetsloggar | Lagras separat som standard och kan användas för aviseringar i nära real tid. Installera lösningen för aktivitets logg analys som ska skrivas till Log Analytics-arbetsyta. Se [samla in och analysera Azure aktivitets loggar i Log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Azure-resurser

| Data | Beskrivning |
|:---|:---|
| Resurs diagnostik | Konfigurera diagnostikinställningar som ska skrivas till diagnostikdata, inklusive mått till en Log Analytics-arbetsyta. Se [strömma Azure-resurshanteraren till Log Analytics](resource-logs-collect-storage.md). |
| Övervakningslösningar | Övervaknings lösningar skriver data som de samlar in i sin Log Analytics-arbetsyta. Se [information om data insamling för hanterings lösningar i Azure](../insights/solutions-inventory.md) för en lista över lösningar. Mer information om hur du installerar och använder lösningar finns [i övervaknings lösningar i Azure Monitor](../insights/solutions.md) . |
| Mått | Skicka plattforms mått för Azure Monitor resurser till en Log Analytics arbets yta för att spara loggdata under längre perioder och för att utföra komplex analys med andra data typer med hjälp av [Kusto-frågespråket](/azure/kusto/query/). Se [strömma Azure-resurshanteraren till Log Analytics](resource-logs-collect-storage.md). |
| Azure Table Storage | Samla in data från Azure Storage där vissa Azure-resurser skriver övervaknings data. Se [använda Azure Blob Storage för IIS och Azure Table Storage för händelser med Log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Virtuella datorer

| Data | Beskrivning |
|:---|:---|
|  Agentdatakällor | Data källor som samlas in från [Windows](agent-windows.md) -och [Linux](../learn/quick-collect-linux-computer.md) -agenter innehåller händelser, prestanda data och anpassade loggar. Se [agent data källor i Azure Monitor](data-sources.md) för att få en lista över data källor och information om konfigurationen. |
| Övervakningslösningar | Övervaknings lösningar skriver data som samlas in från agenter till sin Log Analytics-arbetsyta. Se [information om data insamling för hanterings lösningar i Azure](../insights/solutions-inventory.md) för en lista över lösningar. Mer information om hur du installerar och använder lösningar finns [i övervaknings lösningar i Azure Monitor](../insights/solutions.md) . |
| System Center Operations Manager | Anslut Operations Manager hanterings grupp till Azure Monitor för att samla in händelse-och prestanda data från lokala agenter i loggar. Mer information om den här konfigurationen finns i [anslut Operations Manager till Log Analytics](om-agents.md) . |


### <a name="applications"></a>Program

| Data | Beskrivning |
|:---|:---|
| Begär Anden och undantag | Detaljerad information om program begär Anden och undantag finns i tabellerna för _förfrågningar_, _pageViews_och _undantag_ . Anrop till [externa komponenter](../app/asp-net-dependencies.md) finns i tabellen _beroenden_ . |
| Användning och prestanda | Prestanda för programmet är tillgängligt i tabellerna _requests_, _browserTimings_ och _performanceCounters_ . Data för [anpassade mått](../app/api-custom-events-metrics.md#trackevent) finns i tabellen _customMetrics_ .|
| Spårnings data | Resultat från [distribuerad spårning](../app/distributed-tracing.md) lagras i tabellen _spår_ . |
| Tillgänglighetstester | Sammanfattnings data från [tillgänglighets test](../app/monitor-web-app-availability.md) lagras i tabellen _availabilityResults_ . Detaljerade data från de här testerna finns i separat lagring och nås från Application Insights i Azure Portal. |

### <a name="insights"></a>Insikter

| Data | Beskrivning |
|:---|:---|
| Azure Monitor för containrar | Inventerings-och prestanda data som samlas in av [Azure Monitor för behållare](../insights/container-insights-overview.md). Se [information om container data – samlings information](../insights/container-insights-log-search.md#container-records) för en lista över tabellerna. |
| Azure Monitor för virtuella datorer | Kart-och prestanda data som samlas in av [Azure Monitor for VMS](../insights/vminsights-overview.md). Se [hur du frågar efter loggar från Azure Monitor for VMS](../insights/vminsights-log-search.md) för information om hur du frågar dessa data. |

### <a name="custom"></a>Anpassat 

| Data | Beskrivning |
|:---|:---|
| REST-API | Skriva data till en Log Analytics-arbetsyta från alla REST-klienter. Mer information finns i [Skicka loggdata till Azure monitor med API för HTTP-datainsamling](data-collector-api.md) .
| Logikapp | Skriv data till en Log Analytics arbets yta från ett Logic app-arbetsflöde med åtgärden **Azure Log Analytics data insamlare** . |

### <a name="security"></a>Säkerhet

| Data | Beskrivning |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) lagrar data som samlas in på en Log Analytics arbets yta där den kan analyseras med andra loggdata. Se [data insamling i Azure Security Center](../../security-center/security-center-enable-data-collection.md) för information om arbets ytans konfiguration. |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) lagrar data från data källor till en Log Analytics-arbetsyta. Se [Anslut data källor](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor data plattform](data-platform.md).
- Lär dig mer om [mått i Azure Monitor](data-platform-metrics.md).
- Lär dig mer om [övervaknings data som är tillgängliga](data-sources.md) för olika resurser i Azure.
