---
title: Struktur för Azure Monitor loggar | Microsoft Docs
description: Du behöver en logg fråga för att hämta loggdata från Azure Monitor.  Den här artikeln beskriver hur nya logg frågor används i Azure Monitor och innehåller begrepp som du behöver förstå innan du skapar en.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2020
ms.openlocfilehash: b4882ec9eb8b81ae27a1e8eed2e5b4349fbeac3f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516200"
---
# <a name="structure-of-azure-monitor-logs"></a>Struktur för Azure Monitor loggar
Möjligheten att snabbt få insikter om dina data med hjälp av en [logg fråga](log-query-overview.md) är en kraftfull funktion i Azure Monitor. Om du vill skapa effektiva och användbara frågor bör du förstå några grundläggande begrepp, till exempel var de data du söker finns och hur de struktureras. Den här artikeln innehåller grundläggande begrepp som du behöver för att komma igång.

## <a name="overview"></a>Översikt
Data i Azure Monitor loggar lagras antingen i en Log Analytics arbets yta eller i ett Application Insights program. Båda drivs av [Azure datautforskaren](/azure/data-explorer/) innebär att de utnyttjar sin kraftfulla data motor och frågespråket.

> [!IMPORTANT]
> Om du använder en [arbets yta-baserad Application Insights resurs](../app/create-workspace-resource.md), lagras telemetri i en Log Analytics arbets yta med alla andra loggdata. Tabellerna har bytt namn och struktureras om men har samma information som tabellerna i Application Insights-programmet.

Data i båda arbets ytorna och programmen är indelade i tabeller. var och en lagrar olika typer av data och har en egen unik uppsättning egenskaper. De flesta [data källor](../platform/data-sources.md) skrivs till sina egna tabeller i en Log Analytics arbets yta, medan Application Insights skriver till en fördefinierad uppsättning tabeller i ett Application Insights program. Logg frågor är mycket flexibla så att du enkelt kan kombinera data från flera tabeller och även använda en kors resurs fråga för att kombinera data från tabeller i flera arbets ytor eller för att skriva frågor som kombinerar arbets ytor och program data.

Följande bild visar exempel på data källor som skriver till olika tabeller som används i exempel frågor.

![Tabeller](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Alla data som samlas in av Azure Monitor loggar förutom Application Insights lagras på en [Log Analytics arbets yta](../platform/manage-access.md). Du kan skapa en eller flera arbets ytor beroende på dina specifika krav. [Data källor](../platform/data-sources.md) som aktivitets loggar och resurs loggar från Azure-resurser, agenter på virtuella datorer och data från insikter och övervaknings lösningar kommer att skriva data till en eller flera arbets ytor som du konfigurerar som en del av deras onboarding. Andra tjänster som [Azure Security Center](../../security-center/index.yml) och [Azure Sentinel](../../sentinel/index.yml) använder också en Log Analytics arbets yta för att lagra sina data så att de kan analyseras med hjälp av logg frågor tillsammans med övervaknings data från andra källor.

Olika typer av data lagras i olika tabeller i arbets ytan och varje tabell har en unik uppsättning egenskaper. En standard uppsättning tabeller läggs till i en arbets yta när den skapas och nya tabeller läggs till för olika data källor, lösningar och tjänster, när de har publicerats. Du kan också skapa anpassade tabeller med hjälp av [API: et för data insamling](../platform/data-collector-api.md).

Du kan bläddra bland tabellerna i en arbets yta och deras schema på fliken **schema** i Log Analytics för arbets ytan.

![Schema för arbets yta](media/scope/workspace-schema.png)

Använd följande fråga för att visa en lista över tabellerna i arbets ytan och antalet poster som samlats in till var och en över föregående dag. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Se dokumentationen för varje data källa för information om de tabeller som de skapar. Exempel är artiklar för [agent data källor](../platform/agent-data-sources.md), [resurs loggar](../platform/resource-logs-schema.md)och [övervaknings lösningar](../monitor-reference.md).

### <a name="workspace-permissions"></a>Behörigheter för arbets ytan
Se [utforma en Azure Monitor loggar distribution](../platform/design-logs-deployment.md) för att förstå strategi och rekommendationer för åtkomst kontroll för att ge åtkomst till data i en arbets yta. Förutom att bevilja åtkomst till själva arbets ytan, kan du begränsa åtkomsten till enskilda tabeller med [RBAC på tabell nivå](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Application Insights program

> [!IMPORTANT]
> Om du använder en [arbets yta som baseras på arbets ytans Application Insights resurs](../app/create-workspace-resource.md) telemetri lagras i en Log Analytics arbets yta med alla andra loggdata. Tabellerna har bytt namn och struktureras om men har samma information som tabellerna i en klassisk Application Insights-resurs.

När du skapar ett program i Application Insights skapas automatiskt ett motsvarande program i Azure Monitor loggar. Ingen konfiguration krävs för att samla in data, och programmet kommer automatiskt att skriva övervaknings data, till exempel sidvyer, begär Anden och undantag.

Till skillnad från en Log Analytics arbets yta har ett Application Insights-program en fast uppsättning tabeller. Det går inte att konfigurera andra data källor för att skriva till programmet, så det går inte att skapa fler tabeller. 

| Tabell | Beskrivning | 
|:---|:---|
| availabilityResults | Sammanfattnings data från tillgänglighets test. |
| browserTimings      | Data om klient prestanda, till exempel hur lång tid det tar att bearbeta inkommande data. |
| customEvents        | Anpassade händelser som skapats av ditt program. |
| customMetrics       | Anpassade mått som skapats av ditt program. |
| relation        | Anropar från programmet till andra komponenter (inklusive externa komponenter) som registrerats via TrackDependency () – till exempel anrop till REST API, databas eller ett fil system. |
| undantag          | Undantag som har utlösts av program körningen och fångar både server sidans och klient sidans undantag.|
| pageViews           | Data om varje webbplats-vy med webb läsar information. |
| performanceCounters | Prestanda mått från beräknings resurserna som stöder programmet, till exempel Windows prestanda räknare. |
| autentiseringsbegäran            | Förfrågningar som tagits emot av ditt program. En separat post för begäran loggas till exempel för varje HTTP-begäran som din webbapp tar emot.  |
| Anden              | Detaljerade loggar (spår) har genererats via program kod/loggnings ramverk som registrerats via TrackTrace (). |

Du kan visa schemat för varje tabell på fliken **schema** i Log Analytics för programmet.

![Program schema](media/scope/application-schema.png)

## <a name="standard-properties"></a>Standardegenskaper
Varje tabell i Azure Monitor-loggar har ett eget schema, men det finns standard egenskaper som delas av alla tabeller. Mer information om var och en finns [i standard egenskaper i Azure Monitor loggar](../platform/log-standard-properties.md) .

| Log Analytics-arbetsyta | Application Insights program | Beskrivning |
|:---|:---|:---|
| TimeGenerated | timestamp  | Datum och tid då posten skapades. |
| Typ          | itemType   | Namnet på tabellen som posten hämtades från. |
| _ResourceId   |            | Unik identifierare för den resurs som posten är kopplad till. |
| _IsBillable   |            | Anger om inmatade data är fakturerbara. |
| _BilledSize   |            | Anger storleken i byte på data som ska faktureras. |

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [att använda Log Analytics för att skapa och redigera loggs ökningar](./log-query-overview.md).
- Kolla in en [själv studie kurs om hur du skriver frågor](../log-query/get-started-queries.md) med det nya frågespråket.
