---
title: "Beroende spårning i Azure Application Insights | Microsoft Docs"
description: "Analysera användningen, tillgängligheten och prestanda i din lokala program eller Microsoft Azure-webbapp med Application Insights."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: fe02adafbf96df22462683c69813b05c182d3106
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="set-up-application-insights-dependency-tracking"></a>Konfigurera Application Insights: beroende-spårning
En *beroende* är en extern komponent som anropas av din app. Det är normalt en tjänst som anropas med HTTP, eller en databas eller ett filsystem. [Application Insights](app-insights-overview.md) mäter hur länge programmet väntar på beroenden och hur ofta en beroendeanropet misslyckas. Du kan undersöka specifika anrop och koppla dem till begäranden och undantag.

![exempeldiagram](./media/app-insights-asp-net-dependencies/10-intro.png)

Beroendeövervakare för out box rapporter för närvarande anrop till dessa typer av beroenden:

* Server
  * SQL-databaser
  * ASP.NET-webbprogram och WCF-tjänster som använder HTTP-baserade bindningar
  * Lokal eller fjärransluten HTTP-anrop
  * Azure Cosmos DB, tabell, blob-lagring och kön
* Webbsidor
  * AJAX-anrop

Övervaka fungerar med hjälp av [byte kod instrumentation](https://msdn.microsoft.com/library/z9z62c29.aspx) runt valda metoder. Prestanda försämras är minimal.

Du kan också skriva egna SDK-anrop för att övervaka andra beroenden, både i koden klienten och servern med hjälp av den [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

## <a name="set-up-dependency-monitoring"></a>Konfigurera övervakning av beroende
Partiell beroendeinformation som samlas in automatiskt av den [Application Insights SDK](app-insights-asp-net.md). Installera lämplig agent för värdservern för att få fullständig data.

| Plattform | Installera |
| --- | --- |
| IIS-servern |Antingen [installera statusövervakaren på servern](app-insights-monitor-performance-live-website-now.md) eller [uppgradera ditt program till .NET framework 4.6 eller senare](http://go.microsoft.com/fwlink/?LinkId=528259) och installera den [Application Insights SDK](app-insights-asp-net.md) i din app. |
| Azure-webbapp |I Kontrollpanelen web app, [öppna bladet Application Insights i Kontrollpanelen web app](app-insights-azure-web-apps.md) och välj Installera om du uppmanas. |
| Azure-molntjänst |[Använd startaktivitet](app-insights-cloudservices.md) eller [installera .NET framework 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Var du hittar beroendedata
* [Programavbildningen](#application-map) visualizes beroenden mellan appen och angränsande komponenter.
* [Prestanda, webbläsare och fel blad](#performance-and-blades) visa beroende serverdata.
* [Webbläsare bladet](#ajax-calls) visar AJAX-anrop från användarnas webbläsare.
* [Klicka dig igenom från långsamma eller misslyckade förfrågningar](#diagnose-slow-requests) att kontrollera sambandet anropar.
* [Analytics](#analytics) kan användas för att fråga efter beroendedata.

## <a name="application-map"></a>Programkarta
Programavbildningen fungerar som ett visual-stöd för att identifiera beroenden mellan komponenter i ditt program. Den genereras automatiskt från telemetri från din app. Det här exemplet visar AJAX-anrop från webbläsaren skript och REST-anrop från appen server till två externa tjänster.

![Programkarta](./media/app-insights-asp-net-dependencies/08.png)

* **Navigera i rutorna** relevanta beroende och andra diagram.
* **Fäst kartan** till den [instrumentpanelen](app-insights-dashboards.md), där den fungerar som den ska.

[Läs mer](app-insights-app-map.md).

## <a name="performance-and-failure-blades"></a>Prestanda och fel blad
Bladet prestanda visar beroendeanrop gjorda av programmet server för varaktighet. Det finns en översikt över diagram och en tabell som är segmenterat av anrop.

![Prestandadiagram bladet beroende](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

Klicka dig igenom sammanfattning diagram eller tabell-objekt att söka rådata förekomster av dessa anrop.

![Beroende anropet instanser](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

**Fel antal** visas på den **fel** bladet. Ett fel är alla returkoder som inte är i intervallet 200-399 eller okänd.

> [!NOTE]
> **100% fel?** -Detta innebär förmodligen att du endast får partiella beroendedata. Du behöver [konfigurera beroende övervakning lämpligt för din plattform](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>AJAX-anrop
Bladet webbläsare visar frekvensen varaktighet och fel för AJAX-anrop från [JavaScript i webbsidorna](app-insights-javascript.md). De visas som beroenden.

## <a name="diagnosis"></a>Diagnostisera långsam begäranden
Varje begäran händelse är associerad med beroendeanrop, undantag och andra händelser som spåras medan appen bearbetar begäran. Så om vissa begäranden felaktigt, kan du läsa mer om det är på grund av långsam svar från ett beroende.

Låt oss gå igenom ett exempel på som.

### <a name="tracing-from-requests-to-dependencies"></a>Spåra från begäranden till beroenden
Öppna bladet prestanda och titta på rutnätet begäranden:

![Lista med begäranden med medelvärden och antal](./media/app-insights-asp-net-dependencies/02-reqs.png)

Det översta tar mycket lång tid. Låt oss se om vi ta reda på om tid det tar.

Klicka på den rad som om du vill visa enskilda begäran händelser:

![Lista över begäran förekomster](./media/app-insights-asp-net-dependencies/03-instances.png)

Klicka på valfri tidskrävande instans om du vill granska den ytterligare och rulla remote beroendeanrop som rör denna begäran:

![Hitta anrop till Fjärrberoenden, identifiera onormal varaktighet](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Det ser ut som de flesta tid behandlingen denna begäran har använt vid ett anrop till en lokal tjänst.

Välj den rad som du vill ha mer information:

![Klicka dig igenom remote sambandet att identifiera sällan](./media/app-insights-asp-net-dependencies/05-detail.png)

Ser ut som detta är där problemet. Vi har precisa problemet, så nu vi bara behöver ta reda på varför anropet tar så lång tid.

### <a name="request-timeline"></a>Tidslinje för begäran
I annat fall finns ingen beroendeanropet som är särskilt lång. Men genom att växla till tidslinjevyn kan vi se var fördröjningen uppstod i vår intern bearbetning:

![Hitta anrop till Fjärrberoenden, identifiera onormal varaktighet](./media/app-insights-asp-net-dependencies/04-1.png)

Det verkar vara ett stort mellanrum efter första beroendet anropar, så vi ska titta på vår kod för att avgöra varför det är.

### <a name="profile-your-live-site"></a>Webbplatsen live-profil

Ingen idé där tillfälle? Den [Application Insights profiler](app-insights-profiler.md) spårningar http-anrop till din aktiva plats och visar vilka funktioner i koden tog den längsta tid.

## <a name="failed-requests"></a>Misslyckade begäranden
Misslyckade begäranden kan även vara associerad med misslyckade anrop till beroenden. Vi kan igen, klicka vidare för att hitta orsaken till problemet.

![Klicka på diagrammet för misslyckade begäranden](./media/app-insights-asp-net-dependencies/06-fail.png)

Klicka här för att en förekomst av en misslyckad begäran och titta på dess associerade händelserna.

![Klicka på typ av begäran, instans klickar du på den för att hämta undantagsinformation om för att visa att en annan vy av samma instans.](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analys
Du kan spåra beroenden i den [Log Analytics-frågespråket](https://docs.loganalytics.io/). Här följer några exempel.

* Hitta alla misslyckade beroendeanrop:

```

    dependencies | where success != "True" | take 10
```

* Hitta AJAX-anrop:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Hitta beroendeanrop som är associerade med begäranden:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Hitta AJAX-anrop som är associerade med sidvisningar:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Anpassade beroende spårning
Standard beroende spårning modulen upptäcker automatiskt externa beroenden som databaser och REST API: er. Men du kanske vill vissa ytterligare komponenter behandlas på samma sätt.

Du kan skriva kod som skickar beroendeinformation använder samma [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency) som används av modulerna som standard.

Om du skapar din kod med en sammansättning som du inte skriva själv kan du tid alla anrop, ta reda på vad det gör att din svarstider bidrag. Om du vill att dessa data visas i beroendediagrammen i Application Insights skicka den via `TrackDependency`.

```C#

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
            }
```

Om du vill stänga av modulen standard beroende spårning, ta bort referensen till DependencyTrackingTelemetryModule i [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Felsökning
*Beroende lyckade flagga alltid visar som SANT eller FALSKT.*

*SQL-fråga som inte visas i sin helhet.*

* Uppgradera till den senaste versionen av SDK. Om din version av .NET är mindre än 4.6:
  * IIS-värd: Installera [Application Insights Agent](app-insights-monitor-performance-live-website-now.md) på värdservrar.
  * Azure-webbapp: öppna Application Insights i Kontrollpanelen för web app och installera Application Insights.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
* [Undantag](app-insights-asp-net-exceptions.md)
* [Användar- och siddata](app-insights-javascript.md)
* [Tillgänglighet](app-insights-monitor-web-app-availability.md)
