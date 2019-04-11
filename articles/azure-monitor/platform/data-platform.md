---
title: Azure Monitor-dataplattform | Microsoft Docs
description: Övervakning av data som samlas in av Azure Monitor är uppdelad i mått som är lätt och kan stödja scenarier i nästan realtid och loggar som används för avancerad analys.
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
ms.openlocfilehash: 8883c1e7f2874e1e2e61b8eca122f2ec294c7849
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862071"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor-dataplattform

Aktivera observability över dagens komplexa datormiljöer kör distribuerade program som förlitar sig på både i molnet och lokala tjänster kräver insamling av driftdata från alla lager och alla komponenter i distribuerade system. Du behöver för att kunna utföra djupa insikter på dessa data och konsolidera dem till en enda glasruta med olika perspektiv så att du har stöd för flera av de berörda parter i din organisation.

[Azure Monitor](../overview.md) samlar in och sammanställer data från olika källor i en gemensam dataplattform där det kan användas för analys, visualisering och aviseringar. Det ger en konsekvent upplevelse på data från flera källor, vilket ger dig bättre insikt över alla övervakade resursernas och även med data från andra tjänster som lagrar sina data i Azure Monitor.


![Översikt över Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Observability data i Azure Monitor
Mått, loggar och distribuerade spårningar kallas ofta tre grundpelare för observability. Det här är de olika typerna av data som ett övervakningsverktyg måste samla in och analysera för att tillhandahålla tillräckligt med observability för ett övervakat system. Observability kan uppnås genom att korrelera data från flera pelare och datainsamling i hela uppsättningen av resurser som övervakas. Eftersom Azure Monitor lagrar data från flera källor tillsammans, kan korreleras data och analyseras med hjälp av en gemensam uppsättning verktyg. Det kan även korrelera data över flera Azure-prenumerationer och -klienter, förutom som är värd för andra tjänster.

Azure-resurser genererar en betydande mängd övervakning av data. Azure Monitor konsoliderar dessa data tillsammans med övervakning av data från andra källor till antingen en plattform för mått eller loggar. Var är optimerad för viss övervakningsscenarier och var och en stöder olika funktioner i Azure Monitor. Funktioner som dataanalys, visualiseringar eller aviseringar måste du förstå skillnaderna så att du kan implementera ditt nödvändiga scenario på de mest effektivt och kostnadseffektivt sätt. Insikter i Azure Monitor som [Programinsikter](../app/app-insights-overview.md) eller [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) har analysverktyg som gör att du kan fokusera på övervakning scenariot utan att förstå den skillnader mellan de två typerna av data. 


### <a name="metrics"></a>Mått
[Mått](data-platform-metrics.md) är numeriska värden som beskriver någon aspekt av ett system vid en viss tidpunkt. De har samlats in med jämna mellanrum och identifieras med en tidsstämpel, ett namn, ett värde och en eller flera definierar etiketter. Mått kan aggregeras med olika algoritmer, jämfört med andra mått och analyseras för trender över tid. 

Mått i Azure Monitor lagras i en time series-databas som är optimerad för att analysera data med tidsstämpel. Detta gör mått särskilt lämpade för aviseringar och snabb identifiering av problem. De kan berätta hur systemet fungerar men vanligtvis måste du kombineras med loggar att identifiera de grundläggande orsakerna till problem.

Mått är tillgängliga för interaktiv analys i Azure-portalen med [Metrics Explorer](../app/metrics-explorer.md). De kan läggas till en [Azure-instrumentpanelen](../learn/tutorial-app-dashboards.md) för visualisering i kombination med andra data och används för nästan i realtid [avisering](alerts-metric.md).

Läs mer om Azure Monitor-mått, till exempel sina datakällor i [mått i Azure Monitor](data-platform-metrics.md).

### <a name="logs"></a>Loggar
[Loggar](data-platform-logs.md) är händelser som inträffade i systemet. De kan innehålla olika typer av data och kan vara strukturerade eller fritext formuläret med en tidsstämpel. De kan skapas sporadiskt som händelser i miljön genererar loggposter och genererar en systemet hårt belastad vanligtvis mer loggvolym.

Loggar i Azure Monitor lagras i Log Analytics-arbetsytan som baseras på [Azure Data Explorer](/azure/data-explorer/) som ger en kraftfull analys-motor och [funktionsrikt frågespråk](/azure/kusto/query/). Loggar normalt ger tillräcklig information för att tillhandahålla slutförd kontexten för problemet har identifieras och är värdefull för att identifiera roten fall av problem.

> [!NOTE]
> Det är viktigt att skilja mellan Azure Monitor-loggar och källor av loggdata i Azure. Till exempel prenumeration på händelser i Azure skrivs till en [aktivitetsloggen](activity-logs-overview.md) som du kan visa från Azure Monitor-menyn. De flesta resurser kommer driftsinformation till en [diagnostiklogg](diagnostic-logs-overview.md) som du kan vidarebefordra till olika platser. Azure Monitor-loggar är en log dataplattform som samlar in aktivitetsloggar och diagnostikloggar tillsammans med andra övervakningsdata att tillhandahålla omfattande analyser i dina hela uppsättningen av resurser.


 Du kan arbeta med [logga frågor](../log-query/log-query-overview.md) interaktivt med [Log Analytics](../log-query/portals.md) i Azure-portalen eller lägga till resultatet till en [Azure-instrumentpanelen](../learn/tutorial-app-dashboards.md) för visualisering i kombination med andra data. Du kan också skapa [loggaviseringar](alerts-log.md) som utlöser en avisering baserat på resultatet av en schema-fråga.

Läs mer om Azure Monitor-loggar, inklusive deras datakällor i [loggar i Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Distribuerade spårningar
Spårningar är serie relaterade händelser som följer en användarbegäran via ett distribuerat system. De kan användas för att bestämma beteendet för programkod och prestanda för olika transaktioner. När loggarna skapas ofta av enskilda komponenter i ett distribuerat system, mäter en spårning drift och prestanda för ditt program i hela uppsättningen av komponenter.

Distribuerad spårning i Azure Monitor har aktiverats med den [Application Insights SDK](../app/distributed-tracing.md), och spårningsdata lagras med andra program loggdata som samlas in av Application Insights. På så sätt blir det tillgängligt till samma analysverktyg som andra loggdata inklusive loggfrågor, instrumentpaneler och aviseringar.

Läs mer om distribuerad spårning på [vad som är distribuerad spårning?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Jämför Azure Monitor-mått och loggar

I följande tabell jämförs mått och loggar i Azure Monitor.

| Attribut  | Mått | Loggar |
|:---|:---|:---|
| Fördelar | Enkel och kunna nästan i realtid scenarier, till exempel aviseringar. Perfekt för snabb identifiering av problem. | Analyseras med omfattande frågespråket. Perfekt för detaljerad analys och identifiera grundorsaken. |
| Data | Endast numeriska värden | Eller det numeriska data |
| struktur | Standard uppsättning egenskaper, inklusive exempeltid, resurs som övervakas, ett numeriskt värde. Vissa mått omfattar flera dimensioner för ytterligare definition. | Unik uppsättning egenskaper beroende på loggtypen av. |
| Samling | Samlas in med jämna mellanrum. | Kanske sporadiskt insamlade händelser utlöser en post som ska skapas. |
| Visa i Azure portal | Metrics Explorer | Log Analytics |
| Datakällor är bland annat | Plattformen mätvärden som samlats in från Azure-resurser.<br>Program som övervakas av Application Insights.<br>Anpassad som definieras av programmet eller API: et. | Programmet och diagnostikloggar.<br>Övervakning av lösningar.<br>Agenter och VM-tillägg.<br>Programförfrågningar och undantag.<br>Azure Security Center.<br>API för datainsamling. |

## <a name="collect-monitoring-data"></a>Samla in övervakningsdata
Olika [datakällor för Azure Monitor](data-sources.md) skriver till en Log Analytics-arbetsyta (loggar) eller Azure Monitor metrics databasen (mått) eller båda. Vissa källor skriver direkt till dessa datalager, medan andra kan skriva till en annan plats, till exempel Azure storage och kräver en viss konfiguration att fylla i loggar eller mätvärden. 

Se [mått i Azure Monitor](data-platform-metrics.md) och [loggar i Azure Monitor](data-platform-logs.md) en lista över olika datakällor som fyller på varje typ av.


## <a name="stream-data-to-external-systems"></a>Stream-data till externa system
Förutom att använda verktygen i Azure för att analysera övervakningsdata, kan du ha ett krav att vidarebefordra det till ett externt verktyg, till exempel en säkerhetsinformations- och event management (SIEM)-produkt. Den här vidarebefordran normalt görs direkt från övervakade resurser via [Azure Event Hubs](/azure/event-hubs/). Vissa datakällor kan konfigureras för att skicka data direkt till en event hub du kan använda en annan process, till exempel en Logikapp för att hämta nödvändiga data. Se [Stream Azure-övervakningsdata till en händelsehubb för användning av något externt verktyg](stream-monitoring-data-event-hubs.md) mer information.



## <a name="next-steps"></a>Nästa steg

- Läs mer om [mått i Azure Monitor](data-platform-metrics.md).
- Läs mer om [loggar i Azure Monitor](data-platform-logs.md).
- Lär dig mer om den [övervakningsdata är tillgängliga](data-sources.md) för olika resurser i Azure.
