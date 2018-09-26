---
title: Prestandaräknare i Application Insights | Microsoft Docs
description: Övervaka systemet och anpassad .NET-prestandaräknare i Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2016
ms.author: mbullwin
ms.openlocfilehash: bb1b1e2934de052479f39d40bfe143345e91bc26
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094102"
---
# <a name="system-performance-counters-in-application-insights"></a>Systemprestandaräknare i Application Insights
Windows innehåller en mängd olika [prestandaräknare](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters) , till exempel processor användandet, minne, disk och nätverksanvändning. Du kan också definiera egna. [Application Insights](app-insights-overview.md) kan visa dessa prestandaräknare om ditt program körs under IIS på en lokal värd eller virtuell dator som du har administrativ åtkomst. Diagrammen visar resurserna som är tillgängliga för ditt liveprogram och hjälper dig för att identifiera obalanserad belastning mellan server-instanser.

Prestandaräknare visas i bladet för servrar, som innehåller en tabell som segment av server-instansen.

![Prestandaräknare som rapporteras i Application Insights](./media/app-insights-performance-counters/counters-by-server-instance.png)

(Prestandaräknare är inte tillgängliga för Azure Web Apps. Men du kan [skicka Azure Diagnostics-data till Application Insights](app-insights-azure-diagnostics.md).)

## <a name="view-counters"></a>Visa räknare
Servrar bladet visar en standarduppsättning av prestandaräknare. 

Om du vill se andra räknare, redigera diagrammen på bladet servrar, eller öppna ett nytt [Metrics Explorer](app-insights-metrics-explorer.md) bladet och Lägg till nytt diagram. 

Tillgängliga räknare visas som mått när du redigerar ett diagram.

![Prestandaräknare som rapporteras i Application Insights](./media/app-insights-performance-counters/choose-performance-counters.png)

Om du vill se alla dina mest användbara diagram på samma ställe, skapa en [instrumentpanelen](app-insights-dashboards.md) och fästa dem till den.

## <a name="add-counters"></a>Lägg till räknare
Om prestandaräknare som du vill ha inte visas i listan över mått är eftersom Application Insights SDK inte samla in den i din webbserver. Du kan konfigurera den för att göra detta.

1. Ta reda på vilka räknare som är tillgängliga i servern med hjälp av det här PowerShell-kommando på servern:
   
    `Get-Counter -ListSet *`
   
    (Se [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Öppna ApplicationInsights.config.
   
   * Lägg till Application Insights i din app under utvecklingen och redigera ApplicationInsights.config i projektet och sedan distribuera den till dina servrar.
   * Om du har använt statusövervakaren kan instrumentera en webbapp under körning, hitta ApplicationInsights.config i rotkatalogen för appen i IIS. Uppdatera den det i varje server-instans.
3. Redigera direktivet prestanda insamlaren:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Du kan avbilda standard räknare och du har implementerat själv. `\Objects\Processes` är ett exempel på en standard räknare tillgänglig på alla Windows-datorer. `\Sales(photo)\# Items Sold` är ett exempel på en anpassad räknare som kan implementeras på en webbtjänst. 

Formatet är `\Category(instance)\Counter"`, eller kategorier som inte har instanser, bara `\Category\Counter`.

`ReportAs` krävs för namn på prestandaräknare som inte matchar `[a-zA-Z()/-_ \.]+` – det vill säga de innehåller tecken som inte finns i följande: bokstäver, avrunda hakparenteser, snedstreck, bindestreck, understreck, blanksteg, punkt.

Om du anger en instans, den kommer att samlas in som en dimension ”CounterInstanceName” för den rapporterade måtten.

### <a name="collecting-performance-counters-in-code"></a>Insamling av prestandaräknare i kod
Om du vill samla in prestandaräknare för system och skicka dem till Application Insights, kan du anpassa kodavsnittet nedan:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
Eller så kan du göra samma sak med anpassade mått som du skapade:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Prestandaräknare i Analytics
Du kan söka efter och visa räknare prestandarapporter i [Analytics](app-insights-analytics.md).

Den **performanceCounters** schemat exponerar den `category`, `counter` namn, och `instance` för varje prestandaräknare.  I telemetri för varje program visas endast räknarna för programmet. Till exempel om du vill se vilka räknare är tillgängliga: 

![Prestandaräknare i Application Insights analytics](./media/app-insights-performance-counters/analytics-performance-counters.png)

(”Instans” här refererar till prestandaräknarinstans, inte den roll eller server-instansen. Instans prestandaräknarnamnet vanligtvis segment räknare, till exempel processortid av namnet på process eller program.)

Hämta ett diagram av det tillgängliga minnet under den senaste perioden: 

![Minne tidsdiagram i Application Insights analytics](./media/app-insights-performance-counters/analytics-available-memory.png)

Som andra telemetri **performanceCounters** också har en kolumn `cloud_RoleInstance` värde som anger identiteten för värdserverinstansens som din app körs. Till exempel att jämföra prestanda för din app på olika datorer: 

![Prestanda uppdelat efter rollinstans i Application Insights analytics](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET och Application Insights-antal
*Vad är skillnaden mellan det antal undantag och undantag mått?*

* *Antal undantag* är en system-prestandaräknare. CLR räknar alla hanterade och ohanterade undantag som utlöses och delar det totala antalet i ett exempelintervall med längden på intervallet. Application Insights SDK samlar in det här resultatet och skickar det till portalen.
* *Undantag* antal TrackException rapporterna tas emot av portalen i insamlingsintervall för diagrammet. Den innehåller endast hanterade undantag där du har skrivit TrackException anropar i din kod och inte innehåller alla [ohanterade undantag](app-insights-asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Prestandaräknare i Asp.Net Core-program
Prestandaräknare stöds bara om programmet är inställd på fullständiga .NET Framework. Det finns ingen möjlighet att samla in prestandaräknare för .net Core program.

## <a name="alerts"></a>Aviseringar
Till exempel andra mått, kan du [ställa in en avisering](app-insights-alerts.md) att varna dig om en prestandaräknare går utanför en gräns som du anger. Öppna bladet aviseringar och klicka på Lägg till avisering.

## <a name="next"></a>Nästa steg
* [Beroendespårning](app-insights-asp-net-dependencies.md)
* [Undantagsspårning](app-insights-asp-net-exceptions.md)

