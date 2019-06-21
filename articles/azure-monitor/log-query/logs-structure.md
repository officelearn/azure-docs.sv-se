---
title: Strukturen för Azure Monitor-loggar | Microsoft Docs
description: Du behöver en loggfråga för att hämta loggdata från Azure Monitor.  Den här artikeln beskriver hur nya log frågor används i Azure Monitor och innehåller begrepp som du måste förstå innan du skapar en.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: e243ebbc31f9e941678ac76a83738276995b5ba1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297330"
---
# <a name="structure-of-azure-monitor-logs"></a>Strukturen för Azure Monitor-loggar
Möjligheten att snabbt få insikter om dina data med en [loggfråga](log-query-overview.md) är en kraftfull funktion i Azure Monitor. För att skapa effektiva och användbara frågor, bör du förstå några grundläggande begrepp, till exempel där de data du vill finns och hur den är strukturerad. Den här artikeln innehåller de grundläggande begreppen som du behöver att komma igång.

## <a name="overview"></a>Översikt
Data i Azure Monitor-loggar lagras i Log Analytics-arbetsytan eller ett program med Application Insights. Båda drivs av [Azure Data Explorer](/azure/data-explorer/) vilket innebär att de utnyttja dess kraftfulla motor och fråga språk.

Data i både arbetsytor och program är uppdelad i tabeller, som lagrar olika typer av data och har en egen unik uppsättning egenskaper. De flesta [datakällor](../platform/data-sources.md) skriver till sina egna tabeller i en Log Analytics-arbetsyta, medan Application Insights skriver till en fördefinierad uppsättning tabeller i ett program med Application Insights. Loggfrågor är mycket flexibelt så att du enkelt kombinera data från flera tabeller och även använda en fråga mellan resurser att kombinera data från tabeller i flera arbetsytor eller för att skriva frågor som kombinerar arbetsyta och programdata.

I följande bild visas exempel på datakällorna som skriver till olika tabeller som används i exempelfrågor.

![Tabeller](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Alla data som samlas in av Azure Monitor-loggar med undantag för Application Insights lagras i en [Log Analytics-arbetsyta](../platform/manage-access.md). Du kan skapa en eller flera arbetsytor beroende på dina specifika krav. [Datakällor](../platform/data-sources.md) , till exempel aktivitetsloggar och diagnostik loggar från Azure-resurser, agenter på virtuella datorer och data från insikter och övervakar lösningar skriver data till en eller flera arbetsytor som du konfigurerar som en del av deras onboarding. Andra tjänster som [Azure Security Center](/azure/security-center/) och [Azure Sentinel](/azure/sentinel/) också använda en Log Analytics-arbetsyta för att lagra sina data så att de kan analyseras med hjälp av loggfrågor tillsammans med övervakning av data från andra källor.

Olika typer av data är lagrade i olika tabeller i arbetsytan och varje tabell har en unik uppsättning egenskaper. En standarduppsättning med tabeller läggs till en arbetsyta när den har skapats och nya tabeller har lagts till för olika datakällor, lösningar och tjänster när de är publicerat. Du kan också skapa anpassade tabeller med hjälp av den [Data Collector API](../platform/data-collector-api.md).

Du kan bläddra tabellerna i en arbetsyta och deras schema i den **schemat** fliken i Log Analytics för arbetsytan.

![Schema för arbetsyta](media/scope/workspace-schema.png)

Använd följande fråga för att lista tabellerna i arbetsytan och antalet poster som samlats in i varje under föregående dag. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Finns i dokumentationen för varje datakälla för information om de tabeller som de skapar. Exempel är artiklar för [agenten datakällor](../platform/agent-data-sources.md), [diagnostikloggar](../platform/diagnostic-logs-schema.md), och [övervakningslösningar](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Behörigheter för arbetsytan
Se [behörigheter för arbetsytan och omfång](../platform/manage-access.md#workspace-permissions-and-scope) mer information om att ge åtkomst till data i en arbetsyta. Förutom att bevilja åtkomst till arbetsytan, kan du begränsa åtkomsten till enskilda tabeller med hjälp av [tabell nivå RBAC](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Application Insights-program
När du skapar ett program i Application Insights kan skapas automatiskt ett motsvarande program i Azure Monitor-loggar. Ingen konfiguration krävs för att samla in data och automatiskt skriver du programmet övervakningsdata som sidvisningar, begäranden och undantag.

Till skillnad från en Log Analytics-arbetsyta har ett Application Insights-program en fast uppsättning tabeller. Du kan inte konfigurera andra datakällor för att skriva till programmet så att inga ytterligare tabeller kan skapas. 

| Tabell | Beskrivning | 
|:---|:---|
| availabilityResults | Sammanfattning av data från tillgänglighetstester. |
| browserTimings      | Information om klientens prestanda, till exempel den tid det tar att bearbeta inkommande data. |
| customEvents        | Anpassade händelser som skapas av programmet. |
| customMetrics       | Anpassade mått som skapas av programmet. |
| Beroenden        | Anrop från programmet till externa komponenter. |
| Undantag          | Undantag från programmets körning. |
| pageViews           | Information om varje webbplats visa med webbläsarinformation. |
| PerformanceCounters | Prestandamått från de beräkningsresurser som stöder programmet. |
| Begäranden            | Information om varje programbegäran.  |
| spårningar              | Resultat från distribuerad spårning. |

Du kan visa schemat för varje tabell i den **schemat** fliken i Log Analytics för programmet.

![Schema för](media/scope/application-schema.png)

## <a name="standard-properties"></a>Standardegenskaper
Varje tabell i Azure Monitor-loggar har ett eget schema, men det finns standardegenskaper som delas av alla tabeller. Se [standardegenskaper i Azure Monitor-loggar](../platform/log-standard-properties.md) information om.

| Log Analytics-arbetsyta | Application Insights-program | Beskrivning |
|:---|:---|:---|
| TimeGenerated | timestamp  | Datum och tid då posten skapades. |
| Typ          | ItemType   | Namnet på tabellen posten har hämtats från. |
| _ResourceId   |            | Unik identifierare för resursen posten är associerad med. |
| _IsBillable   |            | Anger om insamlade data är fakturerbara. |
| _BilledSize   |            | Anger storleken i byte av data som kommer att debiteras. |

## <a name="next-steps"></a>Nästa steg
- Läs om hur du använder [loggsökningar i Log Analytics för att skapa och redigera](../log-query/portals.md).
- Kolla in en [självstudiekurs om hur du skriver frågor](../log-query/get-started-queries.md) med hjälp av det nya frågespråket.
