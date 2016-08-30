<properties 
    pageTitle="Konfigurera webbappsanalyser för ASP.NET med Application Insights" 
    description="Konfigurera prestanda-, tillgänglighets- och användningsanalyser för din lokala eller Azure-baserade ASP.NET-webbplats." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/25/2016" 
    ms.author="awills"/>


# Konfigurera Application Insights för ASP.NET

Med [Visual Studio Application Insights](app-insights-overview.md) kan du övervaka ditt liveprogram, [identifiera och diagnostisera prestandaproblem och undantag](app-insights-detect-triage-diagnose.md) och [se hur din app används](app-insights-overview-usage.md).  Lösningen kan användas med appar som finns på dina lokala IIS-servrar eller på virtuella datorer i Azure, samt för Azure-webbappar.


## Innan du börjar

Du behöver:

* Visual Studio 2013 Update 3 eller senare. Senare versioner är att föredra.
* En prenumeration på [Microsoft Azure](http://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](http://live.com). 

Det finns andra artiklar som du kan läsa om du är intresserad av:

* [Instrumentering av en webbapp under körning](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a> 1. Lägga till Application Insights SDK


### Om det är ett nytt projekt …

Kontrollera att Application Insights är markerat när du skapar ett nytt projekt i Visual Studio. 


![Skapa ett ASP.NET-projekt](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


### … eller, om det är ett befintligt projekt

Högerklicka på projektet i Solution Explorer och välj **Lägg till Application Insights Telemetry** eller **Konfigurera Application Insights**.

![Välj Lägg till Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)




## <a name="run"></a> 2. Köra appen

Kör programmet genom att trycka på F5 och prova det: öppna olika sidor för att generera telemetri.

Du ser hur många händelser som har loggats i Visual Studio. 

![Knappen Application Insights visas under felsökningar i Visual Studio.](./media/app-insights-asp-net/54.png)

## 3. Visa din telemetri …

### … i Visual Studio

Öppna Application Insights-fönstret i Visual Studio: Klicka på knappen Application Insights eller högerklicka på ditt projekt i Solution Explorer:

![Knappen Application Insights visas under felsökningar i Visual Studio.](./media/app-insights-asp-net/55.png)

I den här vyn visas telemetri som genereras på serversidan av appen. Experimentera med filtren och klicka på en händelse om du vill visa mer information.

[Lär dig mer om Application Insights-verktygen i Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a> 
### … på portalen

Om du valde *Installera endast SDK* kan du även visa telemetri på Application Insights-webbportalen. 

Portalen innehåller fler diagram, analysverktyg och instrumentpaneler än Visual Studio. 


Öppna din Application Insights-resurs på [Azure-portalen](https://portal.azure.com/).

![Högerklicka på projektet och öppna Azure-portalen](./media/app-insights-asp-net/appinsights-04-openPortal.png)

Portalen öppnas i en vy över telemetrin från din app:
![](./media/app-insights-asp-net/66.png)

* Enskilda händelser visas i **Sök** (1). Data visas här först (och i Live Streaming). Klicka på en händelse om du vill visa dess egenskaper. 
* Sammansatta mätvärden visas i diagrammen (2). Det kan ta en minut eller två innan data visas här. Klicka på ett diagram om du vill öppna ett blad med mer information.

[Läs mer om hur du använder Application Insights på Azure-portalen](app-insights-dashboards.md).

##<a name="land"></a> Vad gjorde ”Lägg till Application Insights”?

Application Insights skickar telemetri från din app till Application Insights-portalen (som finns i Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

Kommandot gör tre saker:

1. Lägger till Application Insights Web SDK NuGet-paketet i projektet. Om du vill visa det i Visual Studio högerklickar du på projektet och väljer Hantera NuGet-paket.
2. Skapar en Application Insights-resurs på [Azure-portalen](https://portal.azure.com/). Det är här som dina data visas. Kommandot hämtar *instrumenteringsnyckeln* som identifierar resursen.
3. Infogar instrumenteringsnyckeln i `ApplicationInsights.config` så att SDK kan skicka telemetri till portalen.

Du kan [utföra dessa steg manuellt](app-insights-asp-net-manual.md) om du vill.


## Nästa steg

| | 
|---|---
|**[Arbeta med Application Insights i Visual Studio](app-insights-visual-studio.md)**<br/>Felsöka med telemetri, köra diagnostiksökningar och gå igenom koden.|![Visual Studio](./media/app-insights-asp-net/61.png)
|**[Arbeta med Application Insights-portalen](app-insights-dashboards.md)**<br/>Instrumentpaneler, kraftfulla verktyg för diagnostik och analys, aviseringar, live-mappning över beroenden för din app och telemetriexport. |![Visual Studio](./media/app-insights-asp-net/62.png)
|**[Lägga till mer information](app-insights-asp-net-more.md)**<br/>Övervaka användning, tillgänglighet, beroenden och undantag. Integrera spårningar från loggningsramverk. Skriv anpassad telemetri. | ![Visual Studio](./media/app-insights-asp-net/64.png)









<!--HONumber=jun16_HO2-->


