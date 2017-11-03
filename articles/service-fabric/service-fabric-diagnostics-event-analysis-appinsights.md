---
title: "Azure Service Fabric-Händelseanalys med Application Insights | Microsoft Docs"
description: "Läs mer om visualisera och analysera händelser med hjälp av Application Insights för övervakning och diagnostik av Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 34f14f42150e46edae2d1352827f96a411117a62
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Händelseanalys och visualisering med Application Insights

Azure Application Insights är en utökningsbar plattform för övervakning av program och diagnostik. Den innehåller en kraftfull analytics och fråga efter verktyget, anpassningsbar instrumentpanel och visualiseringar och ytterligare alternativ, inklusive automatiserade aviseringar. Det är den rekommenderade plattformen för övervakning och diagnostik för Service Fabric-program och tjänster.

## <a name="setting-up-application-insights"></a>Konfigurera Application Insights

### <a name="creating-an-ai-resource"></a>Skapa en AI-resurs

Att skapa en AI resurs head via Azure Marketplace och Sök efter ”Application Insights”. Det ska visas som den första lösningen (det är under kategori ”webb + mobil”). Klicka på **skapa** när du tittar på rätt resurs (bekräfta att din sökväg matchar på bilden nedan).

![Ny Application Insights-resurs](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

Du måste fylla i vissa information för att etablera resursen på rätt sätt. I den *programtyp* fält används ”ASP.NET-webbprogram” om du kommer att använda någon av Service Fabric programmeringsmodeller eller publicera ett .NET-program i klustret. Använd ”Allmänt” om du ska distribuera gäst körbara filer och behållare. I allmänhet som standard med hjälp av ”ASP.NET-webbprogram” till att öppna alternativen i framtiden. Namnet är upp till dina inställningar och varken resursgrupp eller prenumeration är kan ändras efter distributionen av resursen. Vi rekommenderar att AI-resursen finns i samma resursgrupp som klustret. Om du behöver mer information, se [skapa Application Insights-resurs](../application-insights/app-insights-create-new-resource.md)

Du behöver AI Instrumentation för att konfigurera AI med verktyget du händelsen aggregering. När AI-resursen har konfigurerats (tar några minuter när distributionen har validerats), navigera till den och hitta det **egenskaper** avsnitt i det vänstra navigeringsfältet. Ett nytt blad öppnas som visar ett *INSTRUMENTATION NYCKELN*. Om du behöver ändra prenumeration eller resursgrupp för resursen kan utföras här samt.

### <a name="configuring-ai-with-wad"></a>Konfigurera AI med BOMULLSTUSS

>[!NOTE]
>Detta gäller endast Windows kluster för tillfället.

Det finns två sätt att skicka data från BOMULLSTUSS till Azure AI som uppnås genom att lägga till en AI sink BOMULLSTUSS-konfigurationen enligt anvisningarna i [i den här artikeln](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Lägg till en AI Instrumentation nyckel när du skapar ett kluster i Azure-portalen

![Lägga till en AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

När du skapar ett kluster, om diagnostik är aktiverat ””, visas ett valfritt fält för att ange en insikter Programinstrumentering nyckel. Om du klistrar in din AI IKey här kommer AI sink konfigureras automatiskt för dig i Resource Manager-mallen som används för att distribuera klustret.

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

I båda kodavsnitten ovan används namnet ”applicationInsights” för att beskriva sink. Detta är inte ett krav och förutsatt att namnet på sink ingår i ”sänkor”, du kan ange namnet till en sträng.

För närvarande visas loggar från klustret som spåren i AIS Loggvisaren. Eftersom de flesta av spår som kommer från plattformen för nivå ”information” bör överväga du att ändra sink-konfiguration för att endast skicka loggar av typen ”kritiska” eller ”Error”. Detta kan göras genom att lägga till ”kanaler” din mottagare som visas i [i den här artikeln](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Om du använder en felaktig AI IKey i portalen eller i Resource Manager-mall, behöver du manuellt ändra nyckeln och uppdatera klustret eller distribuera den. 

### <a name="configuring-ai-with-eventflow"></a>Konfigurera AI med EventFlow

Om du använder EventFlow att aggregera händelser, se till att importera den `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet-paketet. Följande måste tas med i den *matar ut* avsnitt i den *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

Se till att göra nödvändiga ändringar i dina filter som innehåller alla andra indata (tillsammans med deras respektive NuGet-paket).

## <a name="aisdk"></a>AI. SDK

Vanligtvis rekommenderas att använda EventFlow och BOMULLSTUSS som aggregering lösningar, eftersom de tillåter en mer modulär metod för diagnostik- och övervakning, dvs. Om du vill ändra dina utdata från EventFlow krävs ingen ändring till din faktiska instrumentation en enkel ändring i konfigurationsfilen. Om däremot vill investera i med hjälp av Application Insights och troligen inte att ändra till en annan plattform, bör du fundera på med AIS nya SDK för aggregering händelser och skicka dem till AI. Det innebär att du behöver inte längre konfigurera EventFlow för att skicka data till AI, men i stället installeras den ApplicationInsight Service Fabric-NuGet-paketet. Information om paketet kan hittas [här](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Application Insights-stöd för Mikrotjänster och behållare](https://azure.microsoft.com/app-insights-microservices/) visar några av de nya funktionerna som arbete utförs i (för närvarande i beta), vilket kan du ha bättre out box övervakningsalternativ med AI. Dessa inkluderar beroende spårning (används för att bygga en AppMap av alla tjänster och program i ett kluster och kommunikationen mellan dem) och bättre korrelation av spår som kommer från dina tjänster (hjälper i bättre lokalisera ett problem i arbetsflödet för en app eller tjänst).

Om du utvecklar i .NET och kommer sannolikt att använda vissa av Service Fabric programming modeller och är att använda AI som din plattform för att visualisera och analysera data om händelser och logga sedan rekommenderar vi att du går via AI SDK vägen som arbetsflödet övervakning och diagnostik. Läs [detta](../application-insights/app-insights-asp-net-more.md) och [detta](../application-insights/app-insights-asp-net-trace-logs.md) att komma igång med att AI att samla in och visa loggar.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navigera AI-resurs i Azure-portalen

När du har konfigurerat AI som utdata för händelser och loggar, ska starta information visas i AI-resurs i ett par minuter. Gå till resursen AI som leder dig till instrumentpanelen för AI-resurs. Klicka på **Sök** i Aktivitetsfältet AI att se de senaste spår som har tagit emot och filtrera igenom dem.

*Metrics Explorer* är användbart för att skapa anpassade instrumentpaneler baserat på mått som ditt program, tjänster och klustret kan reporting. Se [utforska mått i Application Insights](../application-insights/app-insights-metrics-explorer.md) att ställa in några diagram för själv baserat på de data du samlar in.

Klicka på **Analytics** tar dig till Application Insights Analytics-portalen där du kan fråga efter händelser och spårningar med större scope och -alternativ. Läs mer om detta i [analyser i Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera aviseringar i AI](../application-insights/app-insights-alerts.md) ska meddelas om ändringar i prestanda eller användning
* [Identifiering i Application Insights för smartkort](../application-insights/app-insights-proactive-diagnostics.md) utför en proaktiv analys av telemetri som skickas till AI att varna dig om potentiella problem med prestanda