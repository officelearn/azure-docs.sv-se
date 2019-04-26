---
title: Azure Service Fabric-Händelseanalys med Application Insights | Microsoft Docs
description: Läs mer om att visualisera och analysera händelser med hjälp av Application Insights för övervakning och diagnostik för Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: f4c620bbb0e17abfacb504866230786a971ff409
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393195"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Händelseanalys och visualisering med Application Insights

En del av Azure Monitor, Application Insights är en utökningsbar plattform för programmet övervakning och diagnostik. Den innehåller en kraftfull analys och frågor till verktyget, anpassningsbar instrumentpanel och visualiseringar och ytterligare alternativ inklusive automatiserade aviseringar. Application Insights-integrering med Service Fabric innehåller verktyg för Visual Studio och Azure portal, samt Service Fabric-specifika mått, ger en omfattande loggning för out-of the box-upplevelse. Även om många loggar som skapas automatiskt och som samlas in för dig med Application Insights, rekommenderar vi att du lägger till ytterligare anpassad loggning dina program att skapa en rikare upplevelse för diagnostik.

Den här artikeln hjälper dig att lösa följande frågor:

* Hur kan jag se vad som händer i Mina program och tjänster och samla telemetri?
* Hur felsöker jag mitt program, särskilt de tjänster som kommunicerar med varandra?
* Hur får jag mått om hur Mina tjänster fungerar, till exempel, sidinläsningstiden, HTTP-begäranden?

Syftet med den här artikeln är att visa hur du få insikter och felsöka från i Application Insights. Om du vill lära dig hur du skapar och konfigurerar Application Insights med Service Fabric finns i denna [självstudien](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Övervakning i Application Insights

Application Insights har en omfattande utanför den box experience när du använder Service Fabric. På översiktssidan översikt innehåller Application Insights viktig information om din tjänst, till exempel svarstid och antal begäranden som bearbetas. Genom att klicka på ”Sök” högst upp, kan du se en lista över de senaste begäranden i ditt program. Dessutom skulle du kunna se här misslyckade förfrågningar och diagnosticera vilka fel som kan ha inträffat.

![Översikt över Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Det finns två typer av poster i listan i den högra rutan i den föregående bilden: begäranden och händelser. Anrop till appens API: ets HTTP-begäranden i det här fallet begärandena och händelser är anpassade händelser, som fungerar som telemetri som du kan lägga till var som helst i din kod. Du kan utforska ytterligare instrumentering av dina program i [Application Insights API för anpassade händelser och mått](../azure-monitor/app/api-custom-events-metrics.md). När du klickar på en begäran skulle visa ytterligare information som visas i följande bild, inklusive data som är specifika för Service Fabric, som har samlats in i Application Insights Service Fabric nuget-paketet. Den här informationen är användbar för felsökning och att känna till vad som är i tillståndet för ditt program och all denna information är sökbart i Application Insights

![Information om Application Insights-begäran](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights har en avsedda vy för att fråga mot alla data som kommer in. Klicka på ”Metrics Explorer” överst på sidan för översikt över att navigera till Application Insights-portalen. Här kan du köra frågor mot anpassade händelser som vi nämnt tidigare, begäranden, undantag, prestandaräknare och andra mått med Kusto-frågespråk. I följande exempel visas alla begäranden under den senaste timmen.

![Information om Application Insights-begäran](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Om du vill fortsätta för att utforska funktionerna i Application Insights-portalen, gå till den [Programinsikter portaldokumentationen](../azure-monitor/app/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurera Application Insights med EventFlow

Om du använder EventFlow att aggregera händelser, se till att importera den `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet-paketet. Följande kod är obligatoriskt i den *matar ut* delen av den *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Se till att göra ändringarna i dina filter, samt innehålla andra indata (tillsammans med deras respektive NuGet-paket).

## <a name="application-insights-sdk"></a>Application Insights SDK

Det rekommenderas att använda EventFlow och WAD som aggregering lösningar, eftersom de gör att en mer modulära metod för diagnostik och övervakning, dvs. Om du vill ändra dina utdata från EventFlow, det krävs ingen ändring till din faktiska instrumentation bara en enkla ändringar i konfigurationsfilen. Om, men du bestämmer dig för att investera i med hjälp av Application Insights och sannolikt inte kan ändra till en annan plattform, bör du fundera på med nya Application Insights-SDK för insamling av händelser och skicka dem till Application Insights. Det innebär att du inte längre att konfigurera EventFlow för att skicka data till Application Insights, men i stället installeras den ApplicationInsight Service Fabric NuGet-paketet. Information om paketet finns [här](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Application Insights-stöd för Mikrotjänster och behållare](https://azure.microsoft.com/blog/app-insights-microservices/) visar några av de nya funktionerna som arbete utförs i (fortfarande för närvarande i beta), som gör att du kan ha bättre out-of the box övervakningsalternativ med Application Insights. Dessa omfattar beroende spårning (används för att skapa en AppMap för alla dina tjänster och program i ett kluster och kommunikationen mellan dem) och bättre korrelation av spårningen från dina tjänster (hjälper i bättre lokalisera ett problem i arbetsflödet för ett program eller tjänst).

Om du utvecklar i .NET och kommer sannolikt att använda några av Service Fabrics programmeringsmodeller och kan använda Application Insights som plattform för att visualisera och analysera händelse-och logga sedan rekommenderar vi att du går via Application Insights SDK-väg som arbetsflödet övervakning och diagnostik. Läs [detta](../azure-monitor/app/asp-net-more.md) och [detta](../azure-monitor/app/asp-net-trace-logs.md) att komma igång med Application Insights om du vill samla in och visa dina loggar.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigera Application Insights-resurs i Azure-portalen

När du har konfigurerat Application Insights som utdata för dina händelser och loggar, starta information visas i Application Insights-resursen på några få minuter. Gå till Application Insights-resurs som tar dig till instrumentpanelen för Application Insights-resurs. Klicka på **Search** i Application Insights-Aktivitetsfältet att se senaste spårningarna som har tagit emot och för att kunna filtrera igenom dem för.

*Metrics Explorer* är användbart för att skapa anpassade instrumentpaneler baserat på mått som dina program, tjänster och -kluster kan reporting. Se [utforska mått i Application Insights](../azure-monitor/app/metrics-explorer.md) att ställa in några diagram för själv baserat på de data som du kan samla in.

Klicka på **Analytics** tar dig till Application Insights Analytics-portalen där du kan fråga efter händelser och spårningar med större omfång och alternativ. Läs mer om detta på [analys i Application Insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera aviseringar i AI](../azure-monitor/app/alerts.md) meddelas om ändringar i prestanda och användning
* [Smart identifiering i Application Insights](../azure-monitor/app/proactive-diagnostics.md) utför en proaktiv analys av telemetri som skickas till Application Insights för att varna dig om potentiella prestandaproblem
