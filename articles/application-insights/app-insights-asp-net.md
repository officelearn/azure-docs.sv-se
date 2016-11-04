---
title: Konfigurera webbappsanalyser för ASP.NET med Application Insights
description: Konfigurera prestanda-, tillgänglighets- och användningsanalyser för din lokala eller Azure-baserade ASP.NET-webbplats.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: daviste

---
# Konfigurera Application Insights för ASP.NET
Med [Visual Studio Application Insights](app-insights-overview.md) kan du övervaka ditt liveprogram, [identifiera och diagnostisera prestandaproblem och undantag](app-insights-detect-triage-diagnose.md) och [se hur din app används](app-insights-overview-usage.md).  Lösningen kan användas med appar som finns på dina lokala IIS-servrar eller på virtuella datorer i Azure, samt för Azure-webbappar.

## Innan du börjar
Du behöver:

* Visual Studio 2013 Update 3 eller senare. Senare versioner är att föredra.
* En prenumeration på [Microsoft Azure](http://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](http://live.com). 

Det finns andra artiklar som du kan läsa om du är intresserad av:

* [Instrumentering av en webbapp under körning](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a> 1. Lägga till Application Insights SDK
### Om det är ett nytt projekt …
Kontrollera att Application Insights är markerat när du skapar ett nytt projekt i Visual Studio. 

![Skapa ett ASP.NET-projekt](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### … eller, om det är ett befintligt projekt
Högerklicka på projektet i Solution Explorer och välj **Lägg till Application Insights Telemetry** eller **Konfigurera Application Insights**.

![Välj Lägg till Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* Är det ett ASP.NET Core-projekt? - [Följ de här anvisningarna om hur du kan åtgärda några kodrader](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 

## <a name="run"></a> 2. Köra appen
Kör programmet genom att trycka på F5 och prova det: öppna olika sidor för att generera telemetri.

Du ser hur många händelser som har loggats i Visual Studio. 

![I Visual Studio visas knappen Application Insights när du felsöker.](./media/app-insights-asp-net/54.png)

## 3. Visa din telemetri …
### … i Visual Studio
Öppna Application Insights-fönstret i Visual Studio: Klicka på knappen Application Insights eller högerklicka på ditt projekt i Solution Explorer:

![Knappen Application Insights visas under felsökningar i Visual Studio.](./media/app-insights-asp-net/55.png)

I den här vyn visas telemetri som genereras på serversidan av appen. Experimentera med filtren och klicka på en händelse om du vill visa mer information.

[Lär dig mer om Application Insights-verktygen i Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a> 

### … på portalen
Om du valde *Installera endast SDK* kan du även visa telemetri på Application Insights-webbportalen. 

Portalen innehåller fler diagram, analysverktyg och instrumentpaneler än Visual Studio. 

Öppna din Application Insights-resurs på [Azure-portalen](https://portal.azure.com/).

![Högerklicka på projektet och öppna Azure-portalen](./media/app-insights-asp-net/appinsights-04-openPortal.png)

Portalen öppnas i en vy över telemetrin från din app:
![](./media/app-insights-asp-net/66.png)

* Den första telemetrin visas i [Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream).
* Enskilda händelser visas i **Sök** (1). Data kan ta några minuter innan informationen visas. Klicka på en händelse om du vill visa dess egenskaper. 
* Sammansatta mätvärden visas i diagrammen (2). Det kan ta en minut eller två innan data visas här. Klicka på ett diagram om du vill öppna ett blad med mer information.

[Läs mer om hur du använder Application Insights på Azure-portalen](app-insights-dashboards.md).

## 4. Publicera appen
Publicera din app på din IIS-server eller i Azure. Bevaka [Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream) och se om allt fungerar som det ska.

Du ser din telemetri byggas upp i Application Insights-portalen, där du kan övervaka mått, söka efter telemetri och konfigurera [instrumentpaneler](app-insights-dashboards.md). Du kan också använda det kraftfulla [Analytics-frågaespråket](app-insights-analytics.md) för att analysera användning och prestanda eller söka efter specifika händelser. 

Du kan också fortsätta att analysera din telemetri i [Visual Studio](app-insights-visual-studio.md) med verktyg som diagnossökning och [trender](app-insights-visual-studio-trends.md).

> [!NOTE]
> Om din app skickar så mycket telemetri att det närmar sig [begränsningsgränserna](app-insights-pricing.md#limits-summary) aktiveras [sampling](app-insights-sampling.md) automatiskt. Sampling minskar mängden telemetri som skickas från din app, samtidigt som korrelerade informationen bevaras i diagnossyfte.
> 
> 

## <a name="land"></a> Vad gjorde ”Lägg till Application Insights”?
Application Insights skickar telemetri från din app till Application Insights-portalen (som finns i Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

Kommandot gör tre saker:

1. Lägger till Application Insights Web SDK NuGet-paketet i projektet. Om du vill visa det i Visual Studio högerklickar du på projektet och väljer Hantera NuGet-paket.
2. Skapar en Application Insights-resurs på [Azure-portalen](https://portal.azure.com/). Det är här som dina data visas. Kommandot hämtar *instrumenteringsnyckeln* som identifierar resursen.
3. Infogar instrumenteringsnyckeln i `ApplicationInsights.config` så att SDK kan skicka telemetri till portalen.

Du kan utföra dessa steg manuellt för [ASP.NET 4](app-insights-asp-net-manual.md) eller [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

### Så här uppgraderar du till framtida SDK-versioner
Om du vill uppgradera till en [ny SDK-version](app-insights-release-notes-dotnet.md) öppnar du NuGet-pakethanteraren igen och filtrerar på installerade paket. Markera Microsoft.ApplicationInsights.Web och välj Uppgradera.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av filen innan du uppgraderar och sammanfogar sedan dina ändringar i den nya versionen.

## Nästa steg
|  |
| --- | --- |
| **[Arbeta med Application Insights i Visual Studio](app-insights-visual-studio.md)**<br/>Felsöka med telemetri, köra diagnostiksökningar och gå igenom koden. |
| **[Arbeta med Application Insights-portalen](app-insights-dashboards.md)**<br/>Instrumentpaneler, kraftfulla verktyg för diagnostik och analys, aviseringar, live-mappning över beroenden för din app och telemetriexport. |
| **[Lägga till mer information](app-insights-asp-net-more.md)**<br/>Övervaka användning, tillgänglighet, beroenden och undantag. Integrera spårningar från loggningsramverk. Skriv anpassad telemetri. |

<!--HONumber=sep16_HO1-->


