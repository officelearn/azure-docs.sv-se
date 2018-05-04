---
title: Azure Service Fabric-Händelseanalys med Application Insights | Microsoft Docs
description: Läs mer om visualisera och analysera händelser med hjälp av Application Insights för övervakning och diagnostik av Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/04/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: fa04e7a3c0d6f19603befed026b316eba6e46eb7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Händelseanalys och visualisering med Application Insights

Azure Application Insights är en utökningsbar plattform för övervakning av program och diagnostik. Den innehåller en kraftfull analytics och fråga efter verktyget, anpassningsbar instrumentpanel och visualiseringar och ytterligare alternativ, inklusive automatiserade aviseringar. Det är den rekommenderade plattformen för övervakning och diagnostik för Service Fabric-program och tjänster. Den här artikeln kan du lösa följande vanliga frågor

* Hur tar jag reda på vad som händer i Mina program och tjänster och samla telemetri
* Hur felsöker jag mina program, särskilt de tjänster som kommunicerar med varandra
* Hur skaffar jag mått om hur Mina tjänster fungerar, till exempel, sidinläsningstiden, HTTP-begäranden

Syftet med den här artikeln är att visa hur du få insikter och felsöka från inom App Insights. Om du vill lära dig hur du skapar och konfigurerar AI med Service Fabric kolla detta [kursen](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-app-insights"></a>Övervakning av App Insights

Application Insights har en omfattande med Service Fabric. På översiktssidan innehåller AI viktig information om tjänsten, till exempel svarstid och antal begäranden som bearbetas. Du kan se en lista över senaste begäranden i ditt program genom att klicka på ”Sök” längst upp. Dessutom kan skulle du kunna finns här misslyckade begäranden och diagnostisera vilka fel uppstod.

![AI-översikt](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Det finns två typer av poster i listan i den högra rutan i föregående bild: begäranden och händelser. Anrop till appens API via HTTP-förfrågningar i det här fallet är och händelser är anpassade händelser, som fungerar som telemetri som du kan lägga till var som helst i koden. Du kan ytterligare utforska instrumentering dina program i [Application Insights API för anpassade händelser och mått](../application-insights/app-insights-api-custom-events-metrics.md). Klicka på en begäran skulle visas ytterligare information som visas i följande bild, inklusive data som är specifika för Service Fabric som samlas in i AI Service Fabric-nuget-paketet. Den här informationen är användbar för felsökning och känna till vad som är i tillståndet för programmet och all den här informationen är sökbara i Application Insights

![AI Frågedetaljer](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights har en avsedda vy för frågor mot alla data som kommer in. Klicka på ”Metrics Explorer” överst på översiktssidan för att navigera till den AI-portalen. Här kan du köra frågor mot anpassade händelser som nämnts, begäranden, undantag, prestandaräknare och andra mått med hjälp av frågespråket Kusto. I följande exempel visas alla begäranden i den senaste timmen.

![AI Frågedetaljer](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Om du vill utforska ytterligare funktioner för App Insights-portalen, gå till den [Programinsikter portaldokumentationen](../application-insights/app-insights-dashboards.md).

### <a name="configuring-ai-with-wad"></a>Konfigurera AI med BOMULLSTUSS

>[!NOTE]
>Detta gäller endast Windows kluster för tillfället.

Det finns två sätt att skicka data från BOMULLSTUSS till Azure AI som uppnås genom att lägga till en AI sink BOMULLSTUSS-konfigurationen enligt anvisningarna i [i den här artikeln](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Lägg till en AI Instrumentation nyckel när du skapar ett kluster i Azure-portalen

![Lägga till en AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

När du skapar ett kluster, om diagnostik är aktiverat ””, visas ett valfritt fält för att ange en insikter Programinstrumentering nyckel. Om du klistrar in AI nyckeln här konfigureras automatiskt AI sink du i Resource Manager-mallen som används för att distribuera klustret.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Lägg till AI Sink i Resource Manager-mall

Lägg till en ”Sink” genom att inkludera följande två ändringar i den ”WadCfg” av Resource Manager-mallen:

1. Lägg till mottagare konfiguration direkt efter den deklarerande av den `DiagnosticMonitorConfiguration` har slutförts:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Inkludera mottagare i den `DiagnosticMonitorConfiguration` genom att lägga till följande rad i den `DiagnosticMonitorConfiguration` av den `WadCfg` (precis före den `EtwProviders` deklareras):

    ```json
    "sinks": "applicationInsights"
    ```

I både de föregående kodfragment används namnet ”applicationInsights” för att beskriva sink. Detta är inte ett krav och förutsatt att namnet på sink ingår i ”sänkor”, du kan ange namnet till en sträng.

För närvarande loggar från klustret visas som **spårningar** i AIS Loggvisaren. Eftersom de flesta av spår som kommer från plattformen för nivå ”information” bör överväga du att ändra sink-konfiguration för att endast skicka loggar av typen ”kritiska” eller ”Error”. Detta kan göras genom att lägga till ”kanaler” din mottagare som visas i [i den här artikeln](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Om du använder en felaktig AI-nyckel i portalen eller i Resource Manager-mall, behöver du manuellt ändra nyckeln och uppdatera klustret eller distribuera den.

### <a name="configuring-ai-with-eventflow"></a>Konfigurera AI med EventFlow

Om du använder EventFlow att aggregera händelser, se till att importera den `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet-paketet. Följande kod är obligatoriskt i den *matar ut* avsnitt i den *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Se till att göra nödvändiga ändringar i dina filter som innehåller alla andra indata (tillsammans med deras respektive NuGet-paket).

## <a name="aisdk"></a>AI. SDK

Det rekommenderas att använda EventFlow och BOMULLSTUSS som aggregering lösningar, eftersom de tillåter en mer modulär metod för diagnostik och ändras inte den faktiska instrumentation kräver övervakning, dvs. Om du vill ändra dina utdata från EventFlow, bara en enkla ändringar i konfigurationsfilen. Om däremot vill investera i med hjälp av Application Insights och troligen inte att ändra till en annan plattform, bör du fundera på med AIS nya SDK för aggregering händelser och skicka dem till AI. Det innebär att du behöver inte längre konfigurera EventFlow för att skicka data till AI, men i stället installeras den ApplicationInsight Service Fabric-NuGet-paketet. Information om paketet kan hittas [här](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Application Insights-stöd för Mikrotjänster och behållare](https://azure.microsoft.com/blog/app-insights-microservices/) visar några av de nya funktionerna som arbete utförs i (för närvarande i beta), vilket kan du ha bättre out box övervakningsalternativ med AI. Dessa inkluderar beroende spårning (används för att bygga en AppMap av alla tjänster och program i ett kluster och kommunikationen mellan dem) och bättre korrelation av spår som kommer från dina tjänster (hjälper i bättre lokalisera ett problem i arbetsflödet för en app eller tjänst).

Om du utvecklar i .NET och kommer sannolikt att använda vissa av Service Fabric programming modeller och är att använda AI som din plattform för att visualisera och analysera data om händelser och logga sedan rekommenderar vi att du går via AI SDK vägen som arbetsflödet övervakning och diagnostik. Läs [detta](../application-insights/app-insights-asp-net-more.md) och [detta](../application-insights/app-insights-asp-net-trace-logs.md) att komma igång med att AI att samla in och visa loggar.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navigera AI-resurs i Azure-portalen

När du har konfigurerat AI som utdata för händelser och loggar, ska starta information visas i AI-resurs i ett par minuter. Gå till resursen AI som leder dig till instrumentpanelen för AI-resurs. Klicka på **Sök** i Aktivitetsfältet AI att se de senaste spår som har tagit emot och filtrera igenom dem.

*Metrics Explorer* är användbart för att skapa anpassade instrumentpaneler baserat på mått som ditt program, tjänster och klustret kan reporting. Se [utforska mått i Application Insights](../application-insights/app-insights-metrics-explorer.md) att ställa in några diagram för själv baserat på de data du samlar in.

Klicka på **Analytics** tar dig till Application Insights Analytics-portalen där du kan fråga efter händelser och spårningar med större scope och -alternativ. Läs mer om detta i [analyser i Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera aviseringar i AI](../application-insights/app-insights-alerts.md) ska meddelas om ändringar i prestanda eller användning
* [Identifiering i Application Insights för smartkort](../application-insights/app-insights-proactive-diagnostics.md) utför en proaktiv analys av telemetri som skickas till AI att varna dig om potentiella problem med prestanda
