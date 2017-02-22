---
title: "Konfigurera webbappsanalyser för ASP.NET med Application Insights | Microsoft Docs"
description: "Konfigurera prestanda-, tillgänglighets- och användningsanalyser för din lokala eller Azure-baserade ASP.NET-webbplats."
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dc95c922b71d18cf791ea98f4ab1a02d2bac2c3b
ms.openlocfilehash: 5103c28047e6d5e7be5f4f3b7933196de7045eeb


---
# <a name="set-up-application-insights-for-aspnet"></a>Konfigurera Application Insights för ASP.NET
Med [Azure Application Insights](app-insights-overview.md) kan du övervaka ditt liveprogram och [identifiera och diagnostisera prestandaproblem och undantag](app-insights-detect-triage-diagnose.md) och [se hur din app används](app-insights-overview-usage.md).  Lösningen kan användas med appar som finns på dina lokala IIS-servrar eller på virtuella datorer i Azure, samt för Azure Web Apps.

## <a name="before-you-start"></a>Innan du börjar
Du behöver:

* Visual Studio 2013 Update 3 eller senare. Senare versioner är att föredra.
* En prenumeration på [Microsoft Azure](http://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](http://live.com). 

Det finns andra artiklar som du kan läsa om du är intresserad av:

* [Instrumentering av en webbapp under körning](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="a-nameidea-1-add-application-insights-sdk"></a><a name="ide"></a> 1. Lägga till Application Insights SDK
### <a name="if-its-a-new-project"></a>Om det är ett nytt projekt …
Kontrollera att Application Insights är markerat när du skapar ett nytt projekt i Visual Studio. 

![Skapa ett ASP.NET-projekt](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### <a name="-or-if-its-an-existing-project"></a>… eller, om det är ett befintligt projekt
Högerklicka på projektet i Solution Explorer och välj **Lägg till Application Insights Telemetry** eller **Konfigurera Application Insights**.

![Välj Lägg till Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* Är det ett ASP.NET Core-projekt? - [Följ de här anvisningarna om hur du kan åtgärda några kodrader](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 

## <a name="a-nameruna-2-run-your-app"></a><a name="run"></a> 2. Köra appen
Kör programmet genom att trycka på F5 och prova det: öppna olika sidor för att generera telemetri.

Du ser hur många händelser som har loggats i Visual Studio. 

![I Visual Studio visas knappen Application Insights när du felsöker.](./media/app-insights-asp-net/54.png)

## <a name="3-see-your-telemetry"></a>3. Visa din telemetri …
### <a name="-in-visual-studio"></a>… i Visual Studio
Öppna Application Insights-fönstret i Visual Studio: Klicka på knappen Application Insights eller högerklicka på ditt projekt i Solution Explorer:

![I Visual Studio visas knappen Application Insights när du felsöker.](./media/app-insights-asp-net/55.png)

I den här vyn (”Data från felsökningssessionen”) visas telemetri som genereras på serversidan av appen. Experimentera med filtren och klicka på en händelse om du vill visa mer information.

* *Ser du inga data? Kontrollera att tidsintervallet är korrekt och klicka på sökikonen.*

[Lär dig mer om Application Insights-verktygen i Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a> 

### <a name="-in-the-portal"></a>… på portalen
Om du valde *Installera endast SDK* kan du även visa telemetri på Application Insights-webbportalen. 

Portalen innehåller fler diagram, analysverktyg och instrumentpaneler än Visual Studio. 

Öppna din Application Insights-resurs – antingen genom att logga in på [Azure Portal](https://portal.azure.com/) och hitta den där, eller högerklicka på projektet i Visual Studio och låt det ta dig dit.

![Högerklicka på projektet och öppna Azure Portal](./media/app-insights-asp-net/appinsights-04-openPortal.png)

* *Fel vid åtkomst? Om du har fler än en uppsättning Microsoft-autentiseringsuppgifter kan du vara inloggad med fel uppsättning. Logga ut och logga in igen i portalen.*

Portalen öppnas med en vy över telemetrin från din app: ![Application Insights översiktssida](./media/app-insights-asp-net/66.png)

Klicka på en panel eller ett diagram för att se mer information.

### <a name="more-detail-in-the-portal"></a>Mer information i portalen

* [**Live Metrics Stream**](app-insights-metrics-explorer.md#live-metrics-stream) visar telemetri nästan omedelbart.

    ![Klicka på Live Stream från översiktsbladet](./media/app-insights-asp-net/livestream.png)

    Öppna Live Stream samtidigt som din app körs, så att de kan ansluta.

    Live Stream visar bara telemetri för en minut efter att det har skickats. För mer historiska undersökningar använder du Search, Metrics Explorer och Analytics. Det kan ta några minuter innan data visas på dessa platser.

* [**Search**](app-insights-diagnostic-search.md) visar enskilda händelser som begäranden, undantag och sidvyer. Du kan filtrera efter händelsetyp, termmatchning och egenskapsvärden. Klicka på en händelse om du vill visa dess egenskaper och relaterade händelser. 

    ![Klicka på Search från översiktsbladet](./media/app-insights-asp-net/search.png)

 * I utvecklingsläge kan du se många beroendehändelser (AJAX). Dessa är synkroniseringar mellan webbläsaren och serveremulatorn. Klicka på beroendefiltret för att dölja dem.
* [**Sammansatta mätvärden** ](app-insights-metrics-explorer.md) som begäran och felnivåer visas i diagrammen. Klicka på ett diagram om du vill öppna ett blad med mer information. Klicka på **redigera**-taggen på ett diagram för att ange filter, storlek och så vidare.
    
    ![Klicka på ett diagram från översiktsbladet](./media/app-insights-asp-net/metrics.png)

[Läs mer om hur du använder Application Insights på Azure Portal](app-insights-dashboards.md).

## <a name="4-publish-your-app"></a>4. Publicera appen
Publicera din app på din IIS-server eller i Azure. Bevaka [Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream) och se om allt fungerar som det ska.

Du ser din telemetri byggas upp i Application Insights-portalen, där du kan övervaka mått, söka efter telemetri och konfigurera [instrumentpaneler](app-insights-dashboards.md). Du kan också använda det kraftfulla [Analytics-frågaespråket](app-insights-analytics.md) för att analysera användning och prestanda eller söka efter specifika händelser. 

Du kan också fortsätta att analysera din telemetri i [Visual Studio](app-insights-visual-studio.md) med verktyg som diagnossökning och [trender](app-insights-visual-studio-trends.md).

> [!NOTE]
> Om din app skickar så mycket telemetri att det närmar sig [begränsningsgränserna](app-insights-pricing.md#limits-summary) aktiveras [sampling](app-insights-sampling.md) automatiskt. Sampling minskar mängden telemetri som skickas från din app, samtidigt som korrelerade informationen bevaras i diagnossyfte.
> 
> 

## <a name="a-namelanda-what-did-add-application-insights-do"></a><a name="land"></a> Vad gjorde ”Lägg till Application Insights”?
Application Insights skickar telemetri från din app till Application Insights-portalen (som finns i Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

Kommandot gör tre saker:

1. Lägger till Application Insights Web SDK NuGet-paketet i projektet. Om du vill visa det i Visual Studio högerklickar du på projektet och väljer Hantera NuGet-paket.
2. Skapar en Application Insights-resurs på [Azure Portal](https://portal.azure.com/). Det är här som dina data visas. Kommandot hämtar *instrumenteringsnyckeln* som identifierar resursen.
3. Infogar instrumenteringsnyckeln i `ApplicationInsights.config` så att SDK kan skicka telemetri till portalen.

Du kan utföra dessa steg manuellt för [ASP.NET 4](app-insights-windows-services.md) eller [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

### <a name="to-upgrade-to-future-sdk-versions"></a>Så här uppgraderar du till framtida SDK-versioner
Om du vill uppgradera till en [ny SDK-version](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases) öppnar du NuGet-pakethanteraren igen och filtrerar på installerade paket. Markera Microsoft.ApplicationInsights.Web och välj Uppgradera.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av filen innan du uppgraderar och sammanfogar sedan dina ändringar i den nya versionen.

## <a name="add-more-telemetry"></a>Lägg till mer telemetri
### <a name="web-pages-and-single-page-apps"></a>Webbsidor och enkelsidiga appar
1. [Lägg till JavaScript-kodavsnitt](app-insights-javascript.md) till dina webbsidor för att förenkla webbläsaren och användarblad med data om sidvyer, inläsningstider, webbläsarundantag, prestanda för AJAX-anrop, antal användare och sessioner.
2. [Kodanpassade händelser](app-insights-api-custom-events-metrics.md) för att beräkna tid eller mäta användaråtgärder.

### <a name="dependencies-exceptions-and-performance-counters"></a>Beroenden, undantag och prestandaräknare
[Installera Statusövervakare](app-insights-monitor-performance-live-website-now.md) på alla dina servrar för att hämta ytterligare telemetri om din app. Det här är vad du får:

* [Prestandaräknare](app-insights-performance-counters.md) - 
  CPU, minne, disk och andra prestandaräknare som rör din app. 
* [Undantag](app-insights-asp-net-exceptions.md) – detaljerad telemetri för vissa undantag.
* [Beroenden](app-insights-asp-net-dependencies.md) – anrop till REST API eller SQL-tjänster. Ta reda på om långsamma svar av externa komponenter orsakar prestandaproblem i din app. (Om din app körs på .NET 4.6, behöver du inte Statusövervakaren för att hämta den här telemetrin.)

### <a name="diagnostic-code"></a>Diagnostikkod
Finns det ett problem? Om du vill infoga kod i din app för att diagnostisera problemet har du flera alternativ:

* [Samla in loggspårningar](app-insights-asp-net-trace-logs.md): Om du redan använder Log4N, NLog eller System.Diagnostics.Trace för att logga spårningshändelser kan utdata skickas till Application Insights så att du kan jämföra med begäranden, söka igenom den och analysera den. 
* [Anpassade händelser och mätvärden](app-insights-api-custom-events-metrics.md): Använd TrackEvent() och TrackMetric() i server- eller webbsidekod.
* [Tagga telemetri med ytterligare egenskaper](app-insights-api-filtering-sampling.md#add-properties)

Använd [Sök](app-insights-diagnostic-search.md) för att söka efter och korrelera specifika händelser och [Analys](app-insights-analytics.md) att utföra mer kraftfulla frågor.

## <a name="alerts"></a>Aviseringar
Var först med att veta om din app har problem. (Vänta inte tills användarna berättar!) 

* [Skapa webbtester](app-insights-monitor-web-app-availability.md) för att kontrollera att webbplatsen visas på webben.
* [Proaktiv diagnostik](app-insights-proactive-diagnostics.md) körs automatiskt (om din app har en viss minsta mängd trafik). Du behöver inte göra något för att ställa in dem. De berättar om din app har ett ovanligt antal misslyckade begäranden.
* [Ställ in mätningsaviseringar](app-insights-alerts.md) för att varna dig om ett mått överskrider ett tröskelvärde. Du kan ställa in dem för anpassade mätningar som du kodar i din app.

Som standard skickas aviseringsmeddelanden till ägaren av Azure-prenumerationen. 

![exempel på varnings-e-post](./media/app-insights-asp-net/alert-email.png)

## <a name="version-and-release-tracking"></a>Spårning av versionen och utgåva
### <a name="track-application-version"></a>Spåra programversion
Kontrollera att `buildinfo.config` genereras av MSBuild-processen. I filen .csproj lägger du till:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

När Application Insights-webbmodulen har fått versionsinformationen läggs **programversionen** automatiskt till som en egenskap för alla telemetriobjekt. Det gör att du kan filtrera baserat på version när du utför [diagnostiksökningar](app-insights-diagnostic-search.md)eller när du [undersöker mätvärden](app-insights-metrics-explorer.md). 

Observera dock att build-versionsnumret endast genereras av MS Build, inte av utvecklarversionen i Visual Studio.

### <a name="release-annotations"></a>Versionsanteckningar
Om du använder Visual Studio Team Services, kan du [få en anteckningsmarkör](app-insights-annotations.md) tillagd i diagrammen när du släpper en ny version.

![exempel på versionsanteckningar](./media/app-insights-asp-net/release-annotation.png)

## <a name="next-steps"></a>Nästa steg
|  |
| --- | --- |
| **[Arbeta med Application Insights i Visual Studio](app-insights-visual-studio.md)**<br/>Felsöka med telemetri, köra diagnostiksökningar och gå igenom koden. |
| **[Arbeta med Application Insights-portalen](app-insights-dashboards.md)**<br/>Instrumentpaneler, kraftfulla verktyg för diagnostik och analys, aviseringar, live-mappning över beroenden för din app och telemetriexport. |
| **[Lägga till mer information](app-insights-asp-net-more.md)**<br/>Övervaka användning, tillgänglighet, beroenden och undantag. Integrera spårningar från loggningsramverk. Skriv anpassad telemetri. |




<!--HONumber=Dec16_HO3-->


