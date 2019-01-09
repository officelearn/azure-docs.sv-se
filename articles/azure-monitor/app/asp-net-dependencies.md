---
title: Beroendespårning i Azure Application Insights | Microsoft Docs
description: Analysera användningen, tillgängligheten och prestanda i din lokala program eller Microsoft Azure-webbapp med Application Insights.
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
ms.openlocfilehash: 48e20df61844d83deb8ac992f783cf227e658aaf
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119993"
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
* [Prestanda, webbläsare och fel bladen](#performance-and-blades) visa beroende serverdata.
* [Bladet webbläsare](#ajax-calls) visar AJAX-anrop från användarnas webbläsare.
* [Klicka dig igenom från långsamt eller misslyckade begäranden](#diagnose-slow-requests) att kontrollera sambandet anropar.
* [Analytics](#analytics) kan användas för att köra frågor mot beroendedata.

## <a name="application-map"></a>Programkarta
Programavbildning fungerar som visuella hjälpmedel att identifiera beroenden mellan komponenterna i ditt program. Det genereras automatiskt från telemetrin från din app. Det här exemplet visar AJAX-anrop av skript för webbläsare och REST-anrop från appen server med två externa tjänster.

![Programkarta](./media/asp-net-dependencies/08.png)

* **Navigera från rutorna** till relevanta beroenden och andra diagram.
* **Fästa kartan** till den [instrumentpanelen](../../azure-monitor/app/app-insights-dashboards.md), där det är helt funktionella.

[Läs mer](../../azure-monitor/app/app-map.md).

## <a name="performance-and-failure-blades"></a>Blad för prestanda och fel
Prestandabladet visar varaktigheten för beroendeanrop från server-app. Det finns en sammanfattning av diagram och en tabell som uppdelat efter anrop.

![Prestandadiagram bladet beroende](./media/asp-net-dependencies/dependencies-in-performance-blade.png)

Klicka dig igenom sammanfattningen diagram eller tabell-objekt att söka raw förekomster av dessa anrop.

![Beroende anrop instanser](./media/asp-net-dependencies/dependency-call-instance.png)

**Felantal** visas på den **fel** bladet. Ett fel är alla returkoder som inte är i intervallet 200 – 399 eller okänd.

> [!NOTE]
> **100% fel?** -Detta innebär förmodligen att du endast får partiella beroendedata. Du behöver [ställa in beroendeövervakning lämpligt för din plattform](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>AJAX-anrop
Bladet webbläsare visar varaktighet och misslyckandegrad för AJAX-anrop från [JavaScript i webbsidor](../../azure-monitor/app/javascript.md). De visas som beroenden.

## <a name="diagnosis"></a> Diagnostisera långsamma begäranden
Varje begäran händelse är associerad med beroendeanrop, undantag och andra händelser som spåras medan appen är bearbetar begäran. Så om vissa begäranden utför felaktigt, kan du ta reda om det är på grund av långsam svar från ett beroende.

Låt oss gå igenom ett exempel på det.

### <a name="tracing-from-requests-to-dependencies"></a>Spåra från begäranden till beroenden
Öppna bladet prestanda och titta på rutnät med begäranden:

![Lista över begäranden med medelvärden och antal](./media/asp-net-dependencies/02-reqs.png)

Den främsta som tar mycket lång tid. Låt oss se om vi kan ta reda på var tid det tar.

Klicka på den rad för att visa enskilda förfrågningar om händelser:

![Lista med förekomsterna av begäran](./media/asp-net-dependencies/03-instances.png)

Klicka på alla tidskrävande instanser för att granska den ytterligare och rulla ned till de fjärranslutna beroendeanrop som rör denna begäran:

![Hitta anrop till Fjärrberoenden, identifiera onormal varaktighet](./media/asp-net-dependencies/04-dependencies.png)

Det ser ut som de flesta av tid behandlingen denna begäran spenderades i ett anrop till en lokal tjänst.

Välj den raden vill ha mer information:

![Klicka dig igenom remote sambandet att identifiera orsaken](./media/asp-net-dependencies/05-detail.png)

Det verkar som det här är var problemet finns. Vi har är utmärkt problemet, så nu vi nyss måste du ta reda på varför anropet tar så lång tid.

### <a name="request-timeline"></a>Tidslinje för begäran
I annat fall finns ingen beroendeanropet som är särskilt lång. Men genom att växla till tidslinjevyn kan vi se var fördröjningen uppstod i våra interna bearbetningen:

![Hitta anrop till Fjärrberoenden, identifiera onormal varaktighet](./media/asp-net-dependencies/04-1.png)

Det verkar vara ett stort mellanrum efter det första beroendet anropar, så vi ska titta på vår kod för att avgöra varför det är.

### <a name="profile-your-live-site"></a>Profilera live-webbplatsen

Ingen aning där tiden går? Den [Application Insights-profileraren](../../azure-monitor/app/profiler.md) spårningar HTTP-anrop till din live-webbplatsen och visar vilka funktioner i din kod tog den längsta tid.

## <a name="failed-requests"></a>Misslyckade förfrågningar
Misslyckade förfrågningar kan också vara kopplad till misslyckade anrop till beroenden. Vi kan igen, klicka vidare för att hitta orsaken till problemet.

![Klicka på diagram över misslyckade begäranden](./media/asp-net-dependencies/06-fail.png)

Klicka här för att en förekomst av en misslyckad begäran och titta på dess associerade händelserna.

![Klicka på en typ av begäran, instansen som ska komma till en annan vy av samma instans, klickar du på den för att få information om undantag.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analytics
Du kan spåra beroenden i den [Log Analytics-frågespråket](https://aka.ms/LogAnalyticsLanguage). Här följer några exempel.

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

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
* [Undantag](../../azure-monitor/app/asp-net-exceptions.md)
* [Användar-och siddata](../../azure-monitor/app/javascript.md)
* [Tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
