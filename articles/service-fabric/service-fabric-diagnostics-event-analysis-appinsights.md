---
title: Azure Service Fabric Event Analysis med application insights
description: Lär dig mer om att visualisera och analysera händelser med hjälp av Application Insights för övervakning och diagnostik av Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 5c80dc9f350fec30469a9bce2ed836c276ef3a7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464751"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Händelseanalys och visualisering med Application Insights

Application Insights är en del av Azure Monitor och är en utökningsbar plattform för programövervakning och diagnostik. Den innehåller ett kraftfullt analys- och frågeverktyg, anpassningsbar instrumentpanel och visualiseringar och ytterligare alternativ, inklusive automatisk avisering. Application Insights integrering med Service Fabric inkluderar verktygsupplevelser för Visual Studio och Azure-portal, samt specifika mått för Service Fabric, vilket ger en omfattande out-of-the-box-loggningsupplevelse. Även om många loggar skapas och samlas in automatiskt för dig med Application Insights rekommenderar vi att du lägger till ytterligare anpassad loggning i dina program för att skapa en rikare diagnostikupplevelse.

Den här artikeln hjälper dig att ta itu med följande vanliga frågor:

* Hur vet jag vad som händer i min applikation och mina tjänster och samlar in telemetri?
* Hur felsöker jag mitt program, särskilt tjänster som kommunicerar med varandra?
* Hur får jag mått på hur mina tjänster presterar, till exempel sidans inläsningstid, HTTP-begäranden?

Syftet med den här artikeln är att visa hur du får insikter och felsöker inifrån Application Insights. Om du vill lära dig hur du konfigurerar Application Insights med Service Fabric kan du läsa den här [självstudien](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Övervakning i Application Insights

Application Insights har en rik out of the box-upplevelse när du använder Service Fabric. På översiktssidan ger Application Insights viktig information om din tjänst, till exempel svarstid och antal behandlade begäranden. Genom att klicka på knappen Sök högst upp kan du se en lista över de senaste förfrågningarna i ditt program. Dessutom skulle du kunna se misslyckade begäranden här och diagnostisera vilka fel som kan ha inträffat.

![Översikt över programinsikter](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

På den högra panelen i föregående bild finns det två huvudtyper av poster i listan: begäranden och händelser. Begäranden görs till appens API via HTTP-begäranden i det här fallet, och händelser är anpassade händelser som fungerar som telemetri som du kan lägga till var som helst i koden. Du kan ytterligare utforska instrumentering av dina program i [Api för Application Insights för anpassade händelser och mått](../azure-monitor/app/api-custom-events-metrics.md). Om du klickar på en begäran visas ytterligare information som visas i följande bild, inklusive data som är specifika för Service Fabric, som samlas in i Application Insights Service Fabric nuget-paketet. Den här informationen är användbar för felsökning och veta vilket tillstånd ditt program är, och all den här informationen är sökbar i Application Insights

![Information om begäran om programinsikter](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights har en angiven vy för att fråga mot alla data som kommer in. Klicka på "Metrics Explorer" högst upp på översiktssidan för att navigera till application insights-portalen. Här kan du köra frågor mot anpassade händelser som nämnts tidigare, begäranden, undantag, prestandaräknare och andra mått med hjälp av Kusto-frågespråket. I följande exempel visas alla begäranden under de senaste 1 timmarna.

![Information om begäran om programinsikter](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Om du vill utforska funktionerna i application insights-portalen ytterligare går du till dokumentationen för [Application Insights-portalen](../azure-monitor/app/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurera programinsikter med EventFlow

Om du använder EventFlow för att samla `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`händelser, se till att importera NuGet-paketet. Följande kod krävs i *avsnittet utdata* i *eventFlowConfig.json:*

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Se till att göra nödvändiga ändringar i dina filter, samt inkludera andra ingångar (tillsammans med deras respektive NuGet-paket).

## <a name="application-insights-sdk"></a>Programinsikter SDK

Vi rekommenderar att du använder EventFlow och WAD som aggregeringslösningar, eftersom de möjliggör en mer modulär metod för diagnostik och övervakning, dvs. enkel ändring av din config-fil. Om du däremot bestämmer dig för att investera i att använda Application Insights och sannolikt inte kommer att ändras till en annan plattform, bör du titta på att använda Application Insights nya SDK för att samla händelser och skicka dem till Application Insights. Det innebär att du inte längre behöver konfigurera EventFlow för att skicka dina data till Application Insights, utan i stället installerar ApplicationInsights Service Fabric NuGet-paket. Information om paketet hittar du [här](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Application Insights-stöd för mikrotjänster och behållare](https://azure.microsoft.com/blog/app-insights-microservices/) visar några av de nya funktioner som bearbetas (för närvarande fortfarande i beta), vilket gör att du kan ha rikare färdiga övervakningsalternativ med Application Insights. Dessa inkluderar beroendespårning (används för att skapa en AppMap över alla dina tjänster och program i ett kluster och kommunikationen mellan dem) och bättre korrelation av spår som kommer från dina tjänster (hjälper till att bättre identifiera ett problem i arbetsflödet för ett program eller en tjänst).

Om du utvecklar i .NET och sannolikt kommer att använda några av Service Fabrics programmeringsmodeller, och är villiga att använda Application Insights som din plattform för att visualisera och analysera händelse- och loggdata, rekommenderar vi att du går via Application Insights SDK-vägen som arbetsflöde för övervakning och diagnostik. Läs [det här](../azure-monitor/app/asp-net-more.md) och [detta](../azure-monitor/app/asp-net-trace-logs.md) för att komma igång med att använda Application Insights för att samla in och visa dina loggar.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigera i application insights-resursen i Azure-portalen

När du har konfigurerat Application Insights som utdata för dina händelser och loggar bör informationen börja visas i application insights-resursen om några minuter. Navigera till application insights-resursen, som tar dig till instrumentpanelen Application Insights.Navigate to the Application Insights resource, which will take you to the Application Insights resource dashboard. Klicka på **Sök** i Aktivitetsfältet Programinsikter om du vill visa de senaste spårningarna som den har tagit emot och för att kunna filtrera igenom dem.

*Metrics Explorer* är ett användbart verktyg för att skapa anpassade instrumentpaneler baserat på mått som dina program, tjänster och kluster kan rapportera. Se [Utforska mätvärden i Application Insights](../azure-monitor/app/metrics-explorer.md) för att skapa några diagram för dig själv baserat på de data du samlar in.

Om du klickar på **Analytics** kommer du till Application Insights Analytics-portalen, där du kan fråga händelser och spårningar med större omfattning och valfrihet. Läs mer om detta på [Analytics i Application Insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Nästa steg

* [Ställ in aviseringar i AI](../azure-monitor/app/alerts.md) som ska meddelas om ändringar i prestanda eller användning
* [Smart Identifiering i Application Insights](../azure-monitor/app/proactive-diagnostics.md) utför en proaktiv analys av den telemetri som skickas till Application Insights för att varna dig om potentiella prestandaproblem
