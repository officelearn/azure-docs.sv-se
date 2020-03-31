---
title: Strukturer för Azure Monitor Logs | Microsoft-dokument
description: Du behöver en loggfråga för att hämta loggdata från Azure Monitor.  I den här artikeln beskrivs hur nya loggfrågor används i Azure Monitor och innehåller begrepp som du måste förstå innan du skapar en.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662084"
---
# <a name="structure-of-azure-monitor-logs"></a>Strukturer för Azure Monitor Logs
Möjligheten att snabbt få insikter i dina data med hjälp av en [loggfråga](log-query-overview.md) är en kraftfull funktion i Azure Monitor. Om du vill skapa effektiva och användbara frågor bör du förstå några grundläggande begrepp, till exempel var de data du vill ha finns och hur de är strukturerade. Den här artikeln innehåller de grundläggande begrepp du behöver för att komma igång.

## <a name="overview"></a>Översikt
Data i Azure Monitor Logs lagras antingen i en Log Analytics-arbetsyta eller ett Application Insights-program. Båda drivs av [Azure Data Explorer](/azure/data-explorer/) vilket innebär att de utnyttjar dess kraftfulla datamotor och frågespråk.

Data i både arbetsytor och program är ordnade i tabeller, som var och en lagrar olika typer av data och har sin egen unika uppsättning egenskaper. De flesta [datakällor](../platform/data-sources.md) skriver till sina egna tabeller på en Log Analytics-arbetsyta, medan Application Insights skriver till en fördefinierad uppsättning tabeller i ett Application Insights-program. Loggfrågor är mycket flexibla så att du enkelt kan kombinera data från flera tabeller och till och med använda en resursfråga för att kombinera data från tabeller i flera arbetsytor eller skriva frågor som kombinerar arbetsyta och programdata.

Följande bild visar exempel på datakällor som skriver till olika tabeller som används i exempelfrågor.

![Tabeller](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Alla data som samlas in av Azure Monitor Logs utom Application Insights lagras i en [Log Analytics-arbetsyta](../platform/manage-access.md). Du kan skapa en eller flera arbetsytor beroende på dina specifika krav. [Datakällor](../platform/data-sources.md) som aktivitetsloggar och resursloggar från Azure-resurser, agenter på virtuella datorer och data från insikter och övervakningslösningar skriver data till en eller flera arbetsytor som du konfigurerar som en del av deras introduktion. Andra tjänster som [Azure Security Center](/azure/security-center/) och Azure [Sentinel](/azure/sentinel/) använder också en Log Analytics-arbetsyta för att lagra sina data så att de kan analyseras med hjälp av loggfrågor tillsammans med övervakningsdata från andra källor.

Olika typer av data lagras i olika tabeller på arbetsytan och varje tabell har en unik uppsättning egenskaper. En standarduppsättning tabeller läggs till på en arbetsyta när den skapas och nya tabeller läggs till för olika datakällor, lösningar och tjänster när de är inbyggda. Du kan också skapa anpassade tabeller med [datainsamlare-API: et](../platform/data-collector-api.md).

Du kan bläddra i tabellerna på en arbetsyta och deras schema på fliken **Schema** i Logganalys för arbetsytan.

![Schema för arbetsyta](media/scope/workspace-schema.png)

Använd följande fråga för att lista tabellerna på arbetsytan och antalet poster som samlats in i varje under föregående dag. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Mer information om de tabeller som de skapar finns i dokumentation för varje datakälla. Exempel på detta är artiklar för [agentdatakällor,](../platform/agent-data-sources.md) [resursloggar](../platform/diagnostic-logs-schema.md)och [övervakningslösningar](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Behörigheter för arbetsyta
Se [Utforma en Azure Monitor Logs-distribution](../platform/design-logs-deployment.md) för att förstå åtkomstkontrollstrategin och rekommendationer för att ge åtkomst till data på en arbetsyta. Förutom att bevilja åtkomst till själva arbetsytan kan du begränsa åtkomsten till enskilda tabeller med hjälp av [RBAC på tabellnivå](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Programinsiktsprogram
När du skapar ett program i Application Insights skapas ett motsvarande program automatiskt i Azure Monitor Logs. Ingen konfiguration krävs för att samla in data och programmet skriver automatiskt övervakningsdata som sidvisningar, begäranden och undantag.

Till skillnad från en Log Analytics-arbetsyta har ett program för programinsikter en fast uppsättning tabeller. Du kan inte konfigurera andra datakällor att skriva till programmet så att inga ytterligare tabeller kan skapas. 

| Tabell | Beskrivning | 
|:---|:---|
| tillgänglighetResultat | Sammanfattande data från tillgänglighetstester. |
| webbläsareTimings      | Data om klientprestanda, till exempel den tid det tar att bearbeta inkommande data. |
| customEvents        | Anpassade händelser som skapats av ditt program. |
| customMetrics       | Anpassade mått som skapats av ditt program. |
| Beroenden        | Anrop från programmet till externa komponenter. |
| Undantag          | Undantag som genereras av programmets körning. |
| Sidvisningar           | Data om varje webbplatsvy med webbläsarinformation. |
| performanceCounters | Prestandamätningar från beräkningsresurserna som stöder programmet. |
| Begäranden            | Information om varje ansökan.  |
| Spår              | Resultat från distribuerad spårning. |

Du kan visa schemat för varje tabell på fliken **Schema** i Logganalys för programmet.

![Programschema](media/scope/application-schema.png)

## <a name="standard-properties"></a>Standardegenskaper
Varje tabell i Azure Monitor Logs har ett eget schema, men det finns standardegenskaper som delas av alla tabeller. Mer information om varje finns [i Standardegenskaper i Azure Monitor Logs.](../platform/log-standard-properties.md)

| Log Analytics-arbetsyta | Programinsiktsprogram | Beskrivning |
|:---|:---|:---|
| TimeGenerated | timestamp  | Datum och tid då posten skapades. |
| Typ          | itemType (artikeltyp)   | Namnet på den tabell som posten hämtades från. |
| _ResourceId   |            | Unik identifierare för den resurs som posten är associerad med. |
| _IsBillable   |            | Anger om intjesterade data kan faktureras. |
| _BilledSize   |            | Anger storleken på byte av data som ska faktureras. |

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om hur du använder [Log Analytics för att skapa och redigera loggsökningar](../log-query/portals.md).
- Kolla in en [handledning om hur du skriver frågor](../log-query/get-started-queries.md) med det nya frågespråket.
