---
title: Loggbaserade och föraggregerade mått i Azure Application Insights | Microsoft-dokument
description: Varför du använder loggbaserade kontra föraggregerade mått i Azure Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 65abc9c7153aaf2973d5927400e27467066098f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275846"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Loggbaserade och föraggregerade mått i Application Insights

I den här artikeln beskrivs skillnaden mellan "traditionella" application insights-mått som baseras på loggar och föraggregerade mått som för närvarande är i offentlig förhandsversion. Båda typerna av mått är tillgängliga för användarna av Application Insights, och var och en ger ett unikt värde i övervakningen av programmets hälsa, diagnostik och analys. Utvecklare som instrumenterar program kan bestämma vilken typ av mått som är bäst lämpad för ett visst scenario, beroende på programmets storlek, förväntad mängd telemetri och affärskrav för måttprecision och aviseringar.

## <a name="log-based-metrics"></a>Loggbaserade mått

Fram till nyligen baserades programövervakningsdatamodellen i Application Insights enbart på ett litet antal fördefinierade typer av händelser, till exempel begäranden, undantag, beroendeanrop, sidvisningar osv. Utvecklare kan använda SDK för att antingen avge dessa händelser manuellt (genom att skriva kod som uttryckligen anropar SDK) eller så kan de förlita sig på automatisk samling av händelser från automatisk instrumentering. I båda fallen lagrar application insights-serverda alla insamlade händelser som loggar, och application insights-bladen i Azure-portalen fungerar som ett analytiskt och diagnostiskt verktyg för att visualisera händelsebaserade data från loggar.

Använda loggar för att behålla en komplett uppsättning händelser kan ge bra analytiskt och diagnostiskt värde. Du kan till exempel få ett exakt antal begäranden till en viss webbadress med antalet olika användare som har gjort dessa samtal. Du kan också få detaljerade diagnostiska spårningar, inklusive undantag och beroendeanrop för alla användarsessioner. Med den här typen av information kan avsevärt förbättra insynen i programmets hälsa och användning, vilket gör det möjligt att minska den tid som krävs för att diagnostisera problem med en app. 

Samtidigt kan det vara opraktiskt (eller till och med omöjligt) att samla in en fullständig uppsättning händelser för program som genererar mycket telemetri. För situationer där volymen av händelser är för hög implementerar Application Insights flera tekniker för minskning av telemetrivolym, till exempel [sampling](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) och [filtrering](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) som minskar antalet insamlade och lagrade händelser. Om du däremot sänker antalet lagrade händelser minskar tyvärr också noggrannheten för de mått som bakom kulisserna måste utföra frågetidsaggregeringar av de händelser som lagras i loggar.

> [!NOTE]
> I Application Insights kallas de mått som baseras på frågetidsaggregering av händelser och mätningar som lagras i loggar loggbaserade mått. Dessa mått har vanligtvis många dimensioner från händelseegenskaperna, vilket gör dem överlägsna för analys, men noggrannheten för dessa mått påverkas negativt av sampling och filtrering.

## <a name="pre-aggregated-metrics"></a>Föraggregerade mått

Förutom loggbaserade mått skickade Application Insights-teamet under hösten 2018 en offentlig förhandsversion av mått som lagras i en specialiserad databas som är optimerad för tidsserier. De nya måtten sparas inte längre som enskilda händelser med många egenskaper. I stället lagras de som föraggregerade tidsserier och endast med nyckeldimensioner. Detta gör de nya måtten överlägsna vid frågetid: hämtning av data sker mycket snabbare och kräver mindre beräkningskraft. Detta möjliggör därför nya scenarier som [nära realtidsavisering om dimensioner av mått,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)mer responsiva [instrumentpaneler](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)med mera.

> [!IMPORTANT]
> Både loggbaserade och föraggregerade mått samexisterar i Application Insights. För att skilja de två, i Application Insights UX kallas nu föraggregerade mått "Standardmått (förhandsversion)", medan de traditionella måtten från händelserna döptes om till "Loggbaserade mått".

De nyare SDK:erna ([Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK eller senare för .NET) föraggade mått under insamlingen innan telemetrivolymreduceringsteknikerna startar. Det innebär att noggrannheten för de nya måtten inte påverkas av sampling och filtrering när du använder de senaste SDK:erna för Application Insights.

För SDK:er som inte implementerar pre-aggregering (det vill än äldre versioner av Application Insights SDK:er eller för webbläsarinstrumentering) fyller application insights-serverdelen fortfarande i de nya måtten genom att samla de händelser som tas emot av programmet Slutpunkt för statistikhändelsesamling. Detta innebär att även om du inte drar nytta av den minskade mängden data som överförs via tråden, kan du fortfarande använda föraggregerade mått och uppleva bättre prestanda och stöd för dimensionellisering i nära realtid med SDK:er som inte föraggregerade mått under insamlingen.

Det är värt att nämna att samlingsslutpunkten föra ihop händelser före intagsprovtagning, vilket innebär att [intagsprovtagning](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) aldrig kommer att påverka noggrannheten för föraggregerade mått, oavsett vilken SDK-version du använder med ditt program.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Använda föraggregering med anpassade mått för Application Insights

Du kan använda pre-aggregering med anpassade mått. De två viktigaste fördelarna är möjligheten att konfigurera och avisera på en dimension av ett anpassat mått och minska mängden data som skickas från SDK till application insights-samlingsslutpunkten.

Det finns flera [sätt att skicka anpassade mått från Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Om din version av SDK erbjuder [metoderna GetMetric och TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) är detta det bästa sättet att skicka anpassade mått, eftersom det i det här fallet sker pre-aggregering inuti SDK, inte bara minska mängden data som lagras i Azure, men också volymen av data som överförs från SDK till Application Insights. Annars använder du [metoden trackMetric,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) som föragrerar måtthändelser under datainmatning.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Anpassade måttdimensioner och pre-aggregering

Alla mått som du skickar med [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) eller [GetMetric och TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) API-anrop lagras automatiskt i både loggar och mått butiker. Men medan den loggbaserade versionen av ditt anpassade mått alltid behåller alla dimensioner, lagras den föraggregerade versionen av måttet som standard utan dimensioner. Du kan aktivera samling av dimensioner av anpassade mått på [fliken användning och uppskattad kostnad](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) genom att markera "Aktivera avisering om anpassade måttdimensioner": 

![Användning och uppskattad kostnad](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Varför är insamling av anpassade måttdimensioner inaktiverad som standard?

Samlingen av anpassade måttdimensioner är inaktiverad som standard eftersom det i framtiden kommer att faktureras anpassade mått med dimensioner separat från Application Insights, medan lagring av icke-dimensionella anpassade mått förblir gratis (upp till en kvot) . Du kan läsa om de kommande prismodelländringarna på vår officiella [prissida.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Skapa diagram och utforska loggbaserade och standardpreaggerade mått

Använd [Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) för att rita diagram från föraggregerade och loggbaserade mått och för att skapa instrumentpaneler med diagram. När du har valt önskad Application Insights-resurs använder du namnområdesväljaren för att växla mellan standardvärden (förhandsgranskning) och loggbaserade mått, eller välj ett anpassat måttnamnområde:

![Måttnamnområde](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Nästa steg

* [Varning i nära realtid](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric och TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)
