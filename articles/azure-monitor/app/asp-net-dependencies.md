---
title: Beroendespårning i Azure Application Insights | Microsoft Docs
description: Analysera användningen, tillgängligheten och prestanda i din lokala eller Microsoft Azure-webbprogram med Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: c77b5810164aef7508f717a0f75d90cf6cba2089
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273115"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Konfigurera Application Insights: Beroendespårning
En *beroende* är en extern komponent som anropas av din app. Det är normalt en tjänst som kallas via HTTP, eller en databas eller ett filsystem. [Application Insights](../../azure-monitor/app/app-insights-overview.md) mäter hur länge ditt program väntar beroenden och hur ofta en beroendeanropet misslyckas. Du kan undersöka specifika anrop och koppla dem till begäranden och undantag.

Beroendeövervakare för out-of the box rapporterar för närvarande anrop till dessa typer av beroenden:

* Server
  * SQL-databaser
  * ASP.NET webb- och WCF-tjänster som använder HTTP-baserade bindningar
  * Lokala eller fjärranslutna HTTP-anrop
  * Azure Cosmos DB, tabell, blob-lagring och kö 
* Webbsidor
  * AJAX-anrop

Övervaka fungerar med hjälp av [byte kod instrumentation](https://msdn.microsoft.com/library/z9z62c29.aspx) cirka Välj metoder eller baserat på DiagnosticSource återanrop (i den senaste .NET SDK: er) från .NET Framework. Prestanda försämras är minimal.

Du kan också skriva egna SDK-anrop för att övervaka andra beroenden, både i koden klienten och servern med hjälp av den [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

> [!NOTE]
> Azure Cosmos DB spåras automatiskt endast om [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) används. TCP-läge samlas inte in av Application Insights.

## <a name="set-up-dependency-monitoring"></a>Konfigurera beroendeövervakning
Partiell beroendeinformation som samlas in automatiskt av den [Application Insights SDK](asp-net.md). För att få fullständig data kan du installera lämplig agent för värdservern.

| Plattform | Installera |
| --- | --- |
| IIS-Server |Antingen [installera Status Monitor på servern](../../azure-monitor/app/monitor-performance-live-website-now.md) eller [uppgradera programmet till .NET framework 4.6 eller senare](https://go.microsoft.com/fwlink/?LinkId=528259) och installera den [Application Insights SDK](asp-net.md) i din app. |
| Azure-webbapp |I din Kontrollpanelen för webbappar, [öppnar du bladet Application Insights i din Kontrollpanelen för webbappar](../../azure-monitor/app/azure-web-apps.md) och välj Installera om du tillfrågas. |
| Azure Cloud Service |[Använd startåtgärd](../../azure-monitor/app/cloudservices.md) eller [installera .NET framework 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Var du hittar data för programberoende
* [Programavbildning](#application-map) hjälper dig att visualisera beroenden mellan din app och Närliggande komponenter.
* [Prestanda, webbläsare och fel bladen](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance) visa beroende serverdata.
* [Bladet webbläsare](#ajax-calls) visar AJAX-anrop från användarnas webbläsare.
* Klicka dig igenom från långsamma eller misslyckade begäranden om att kontrollera deras beroendeanrop.
* [Analytics](#analytics) kan användas för att köra frågor mot beroendedata.

## <a name="application-map"></a>Programkarta
Programavbildning fungerar som visuella hjälpmedel att identifiera beroenden mellan komponenterna i ditt program. Det genereras automatiskt från telemetrin från din app. Det här exemplet visar AJAX-anrop av skript för webbläsare och REST-anrop från appen server med två externa tjänster.

![Programkarta](./media/asp-net-dependencies/cloud-rolename.png)

* **Navigera från rutorna** till relevanta beroenden och andra diagram.
* **Fästa kartan** till den [instrumentpanelen](../../azure-monitor/app/app-insights-dashboards.md), där det är helt funktionella.

[Läs mer](../../azure-monitor/app/app-map.md).

## <a name="performance-and-failure-blades"></a>Blad för prestanda och fel
Prestandabladet visar varaktigheten för beroendeanrop från server-app.

**Felantal** visas på den **fel** bladet. Ett fel är alla returkoder som inte är i intervallet 200 – 399 eller okänd.

> [!NOTE]
> **100% fel?** -Detta innebär förmodligen att du endast får partiella beroendedata. Du behöver [ställa in beroendeövervakning lämpligt för din plattform](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>AJAX-anrop
Bladet webbläsare visar varaktighet och misslyckandegrad för AJAX-anrop från [JavaScript i webbsidor](../../azure-monitor/app/javascript.md). De visas som beroenden.

## <a name="diagnosis"></a> Diagnostisera långsamma begäranden
Varje begäran händelse är associerad med beroendeanrop, undantag och andra händelser som spåras medan appen är bearbetar begäran. Så om vissa begäranden utför felaktigt, kan du ta reda om det är på grund av långsam svar från ett beroende.

### <a name="profile-your-live-site"></a>Profilera live-webbplatsen

Ingen aning där tiden går? Den [Application Insights-profileraren](../../azure-monitor/app/profiler.md) spårningar HTTP-anrop till din live-webbplatsen och visar vilka funktioner i din kod tog den längsta tid.

## <a name="analytics"></a>Analytics
Du kan spåra beroenden i den [Kusto-frågespråket](/azure/kusto/query/). Här följer några exempel.

* Hitta alla misslyckade beroendeanrop:

```

    dependencies | where success != "True" | take 10
```

* Hitta AJAX-anrop:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Hitta beroendeanrop som associeras med förfrågningar:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Hitta AJAX-anrop som är associerade med sidvyer:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Anpassade beroendespårning
Standard beroende-spårningsmodulen identifierar automatiskt externa beroenden, till exempel databaser och REST API: er. Men du vissa ytterligare komponenter så att de behandlas på samma sätt.

Du kan skriva kod som skickar beroendeinformation, med samma [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) som används för moduler som standard.

Om du bygger din kod med en sammansättning som du inte har skrivit själv kan du till exempel tid alla anrop till det, att ta reda på hur stora bidrag kommer görs till din svarstider. För att få dessa data visas i beroendediagrammen i Application Insights ska skicka den via `TrackDependency`.

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

Ta bort referensen till DependencyTrackingTelemetryModule i om du vill stänga av standard beroende-spårningsmodulen [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Felsökning
*Beroende lyckades flagga alltid visar true eller false.*

*SQL-fråga som inte visas i sin helhet.*

Se tabellen nedan och se till att du har valt rätt konfiguration för att aktivera beroendeövervakning för ditt program.

| Plattform | Installera |
| --- | --- |
| IIS-Server |Antingen [installera Status Monitor på servern](../../azure-monitor/app/monitor-performance-live-website-now.md). Eller [uppgradera programmet till .NET framework 4.6 eller senare](https://go.microsoft.com/fwlink/?LinkId=528259) och installera den [Application Insights SDK](asp-net.md) i din app. |
| IIS Express |Använd IIS-servern i stället. |
| Azure-webbapp |I din Kontrollpanelen för webbappar, [öppnar du bladet Application Insights i din Kontrollpanelen för webbappar](../../azure-monitor/app/azure-web-apps.md) och välj Installera om du tillfrågas. |
| Azure Cloud Service |[Använd startåtgärd](../../azure-monitor/app/cloudservices.md) eller [installera .NET framework 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="next-steps"></a>Nästa steg
* [Undantag](../../azure-monitor/app/asp-net-exceptions.md)
* [Användar- och siddata](../../azure-monitor/app/javascript.md)
* [Tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
