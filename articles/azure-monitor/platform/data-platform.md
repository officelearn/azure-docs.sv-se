---
title: Azure Monitor-dataplattform | Microsoft-dokument
description: Övervakningsdata som samlas in av Azure Monitor är uppdelade i mått som är lätta och kan stödja scenarier och loggar i nära realtid som används för avancerad analys.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 58f542238c952088777ed9809b57dae3cdb9cf12
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457271"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor-dataplattform

För att möjliggöra observerbarhet i dagens komplexa datormiljöer som kör distribuerade program som är beroende av både molntjänster och lokala tjänster kräver insamling av driftsdata från varje lager och varje komponent i det distribuerade systemet. Du måste kunna utföra djupa insikter om dessa data och konsolidera dem till en enda glasruta med olika perspektiv för att stödja de många intressenterna i din organisation.

[Azure Monitor](../overview.md) samlar in och sammanställer data från en mängd olika källor till en gemensam dataplattform där den kan användas för analys, visualisering och aviseringar. Det ger en konsekvent upplevelse utöver data från flera källor, vilket ger dig djup insikt i alla dina övervakade resurser och även med data från andra tjänster som lagrar sina data i Azure Monitor.


![Översikt över Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Observabilitetsdata i Azure Monitor
Mått, loggar och distribuerade spår kallas ofta de tre pelarna för observerbarhet. Dessa är de olika typer av data som ett övervakningsverktyg måste samla in och analysera för att ge tillräcklig observerbarhet av ett övervakat system. Observerbarhet kan uppnås genom att korrelera data från flera pelare och samla data över hela uppsättningen resurser som övervakas. Eftersom Azure Monitor lagrar data från flera källor tillsammans kan data korreleras och analyseras med hjälp av en gemensam uppsättning verktyg. Det korrelerar också data över flera Azure-prenumerationer och klienter, förutom att vara värd för data för andra tjänster.

Azure-resurser genererar en betydande mängd övervakningsdata. Azure Monitor konsoliderar dessa data tillsammans med övervakning av data från andra källor till antingen en mått- eller loggplattform. Var och en är optimerad för särskilda övervakningsscenarier och var och en stöder olika funktioner i Azure Monitor. Funktioner som dataanalys, visualiseringar eller aviseringar kräver att du förstår skillnaderna så att du kan implementera det scenario som krävs på det mest effektiva och kostnadseffektiva sättet. Insikter i Azure Monitor, till exempel [Application Insights](../app/app-insights-overview.md) eller [Azure Monitor för virtuella datorer,](../insights/vminsights-overview.md) har analysverktyg som gör att du kan fokusera på det specifika övervakningsscenariot utan att behöva förstå skillnaderna mellan de två typerna av data. 


### <a name="metrics"></a>Mått
[Mått](data-platform-metrics.md) är numeriska värden som beskriver någon aspekt av ett system vid en viss tidpunkt. De samlas in med jämna mellanrum och identifieras med en tidsstämpel, ett namn, ett värde och en eller flera definierande etiketter. Mått kan aggregeras med hjälp av en mängd olika algoritmer, jämfört med andra mått, och analyseras för trender över tid. 

Mått i Azure Monitor lagras i en tidsseriedatabas som är optimerad för att analysera tidsstämplade data. Detta gör mått särskilt lämpade för aviseringar och snabb identifiering av problem. De kan berätta hur systemet fungerar men vanligtvis måste kombineras med loggar för att identifiera orsaken till problem.

Mått är tillgängliga för interaktiv analys i Azure-portalen med [Azure Metrics Explorer](../platform/metrics-getting-started.md). De kan läggas till på en [Azure-instrumentpanel](../learn/tutorial-app-dashboards.md) för visualisering i kombination med andra data och användas för aviseringar i nästan [realtid](alerts-metric.md).

Läs mer om Azure Monitor-mått, inklusive deras datakällor i [mått i Azure Monitor](data-platform-metrics.md).

### <a name="logs"></a>Loggar
[Loggar](data-platform-logs.md) är händelser som inträffade i systemet. De kan innehålla olika typer av data och kan vara strukturerad eller fri form text med en tidsstämpel. De kan skapas sporadiskt eftersom händelser i miljön genererar loggposter, och ett system under tung belastning genererar vanligtvis mer loggvolym.

Loggar i Azure Monitor lagras i en Log Analytics-arbetsyta som baseras på [Azure Data Explorer](/azure/data-explorer/) som tillhandahåller en kraftfull analysmotor och ett rikt [frågespråk](/azure/kusto/query/). Loggar ger vanligtvis tillräckligt med information för att ge fullständig kontext av problemet identifieras och är värdefulla för att identifiera rot fall av problem.

> [!NOTE]
> Det är viktigt att skilja mellan Azure Monitor Loggar och källor till loggdata i Azure. Till exempel skrivs prenumerationsnivåhändelser i Azure till en [aktivitetslogg](platform-logs-overview.md) som du kan visa från Azure Monitor-menyn. De flesta resurser skriver operativ information till en [resurslogg](platform-logs-overview.md) som du kan vidarebefordra till olika platser. Azure Monitor Logs är en loggdataplattform som samlar in aktivitetsloggar och resursloggar tillsammans med andra övervakningsdata för att ge djup analys över hela din uppsättning resurser.


 Du kan arbeta med [loggfrågor](../log-query/log-query-overview.md) interaktivt med [Log Analytics](../log-query/portals.md) i Azure-portalen eller lägga till resultaten i en [Azure-instrumentpanel](../learn/tutorial-app-dashboards.md) för visualisering i kombination med andra data. Du kan också skapa [loggaviseringar](alerts-log.md) som utlöser en avisering baserat på resultatet av en schemafråga.

Läs mer om Azure Monitor-loggar, inklusive deras datakällor i [Loggar i Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Distribuerade spår
Spår är serier av relaterade händelser som följer en användarbegäran via ett distribuerat system. De kan användas för att bestämma beteendet hos programkod och prestanda för olika transaktioner. Medan loggar ofta skapas av enskilda komponenter i ett distribuerat system, mäter en spårning hur ditt program fungerar och prestanda över hela uppsättningen komponenter.

Distribuerad spårning i Azure Monitor är aktiverad med [Application Insights SDK](../app/distributed-tracing.md)och spårningsdata lagras med andra programloggdata som samlas in av Application Insights. Detta gör den tillgänglig för samma analysverktyg som andra loggdata, inklusive loggfrågor, instrumentpaneler och aviseringar.

Läs mer om distribuerad spårning på [Vad är distribuerad spårning?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Jämföra Azure Monitor-mått och loggar

I följande tabell jämförs mått och loggar i Azure Monitor.

| Attribut  | Mått | Loggar |
|:---|:---|:---|
| Fördelar | Lätt vikt och kan nästan realtid scenarier såsom aviseringar. Perfekt för snabb upptäckt av problem. | Analyseras med rikt frågespråk. Perfekt för djupanalys och identifiering av grundorsak. |
| Data | Endast numeriska värden | Text- eller numeriska data |
| Struktur | Standarduppsättning egenskaper inklusive exempeltid, resurs som övervakas, ett numeriskt värde. Vissa mått innehåller flera dimensioner för ytterligare definition. | Unik uppsättning egenskaper beroende på loggtyp. |
| Samling | Samlas in med jämna mellanrum. | Kan samlas in sporadiskt som händelser utlösa en post som ska skapas. |
| Visa i Azure-portalen | Metrics Explorer | Log Analytics |
| Datakällor inkluderar | Plattformsmått som samlats in från Azure-resurser.<br>Program som övervakas av Application Insights.<br>Anpassad definierad av program eller API. | Program- och resursloggar.<br>Övervakningslösningar.<br>Agenter och VM-tillägg.<br>Begäranden och undantag för program.<br>Azure Security Center.<br>Api för datainsamlare. |

## <a name="collect-monitoring-data"></a>Samla in övervakningsdata
Olika [datakällor för Azure Monitor](data-sources.md) skriver till antingen en Log Analytics-arbetsyta (loggar) eller Azure Monitor-måttdatabasen (Mått) eller båda. Vissa källor skriver direkt till dessa datalager, medan andra kan skriva till en annan plats, till exempel Azure-lagring och kräva viss konfiguration för att fylla i loggar eller mått. 

Se [Mått i Azure Monitor](data-platform-metrics.md) och loggar i Azure [Monitor](data-platform-logs.md) för en lista över olika datakällor som fyller i varje typ.


## <a name="stream-data-to-external-systems"></a>Strömma data till externa system
Förutom att använda verktygen i Azure för att analysera övervakningsdata kan du ha ett krav på att vidarebefordra dem till ett externt verktyg, till exempel en SIEM-produkt (Security Information and Event Management). Den här vidarebefordran görs vanligtvis direkt från övervakade resurser via [Azure Event Hubs](/azure/event-hubs/). Vissa källor kan konfigureras för att skicka data direkt till en händelsehubb medan du kan använda en annan process, till exempel en logikapp för att hämta nödvändiga data. Mer information finns [i Strömma Azure-övervakningsdata till en händelsehubb för förbrukning av ett externt verktyg.](stream-monitoring-data-event-hubs.md)



## <a name="next-steps"></a>Nästa steg

- Läs mer om [mått i Azure Monitor](data-platform-metrics.md).
- Läs mer om [Loggar i Azure Monitor](data-platform-logs.md).
- Lär dig mer om [övervakningsdata som är tillgängliga](data-sources.md) för olika resurser i Azure.
