---
title: "Konfigurera webbappsanalyser för ASP.NET med Application Insights | Microsoft Docs"
description: "Konfigurera prestanda-, tillgänglighets- och användningsanalyser för din lokala eller Azure-baserade ASP.NET-webbplats."
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: ae869be6ed9f304629498f416ffdda96252bdf9c
ms.lasthandoff: 03/16/2017


---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Konfigurera Application Insights för din ASP.NET-webbplats
Med [Azure Application Insights](app-insights-overview.md) kan du övervaka tillämpningen i realtid samt [identifiera och diagnostisera prestandaproblem och undantag](app-insights-detect-triage-diagnose.md). Det underlättar även om du vill [identifiera hur din app används](app-insights-overview-usage.md). Den fungerar för funktionen Web Apps i Azure App Service, samt appar som finns på de lokala IIS-servrarna eller på virtuella molndatorer.

## <a name="before-you-start"></a>Innan du börjar
Du behöver:

* Visual Studio 2013 Update 3 eller senare. Senare versioner är att föredra.
* En prenumeration på [Microsoft Azure](http://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](http://live.com).

Det finns andra artiklar som du kan läsa om du är intresserad av följande:

* [Instrumentering av en webbapp under körning](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a> Steg 1: Lägg till Application Insights SDK

Högerklicka på webbapp-projektet i Solution Explorer och välj **Lägg till**, **Application Insights Telemetry...** eller **Konfigurera Application Insights**.

![Skärmbild av Solution Explorer med Lägg till Application Insights Telemetry markerat](./media/app-insights-asp-net/appinsights-03-addExisting.png)

(I Visual Studio 2015 finns det också ett alternativ för att lägga till Application Insights i dialogrutan Nytt projekt.)

Fortsätt till konfigurationssidan för Application Insights:

![Skärmbild av sidan Register your app with Application Insights (Registrera din app med Application Insights)](./media/app-insights-asp-net/visual-studio-register-dialog.png)

1. Välj det konto och din prenumeration som du använder för att få åtkomst till Azure.
2. Markera resursen i Azure där du vill visa data från din app. Vanligtvis skapar du en separat resurs för en varje app. Om du vill ange resursgruppen eller den plats där dina data är lagrade klickar du på **Konfigurera inställningar**. Resursgrupper används för att styra dataåtkomst. Om du exempelvis har flera appar som ingår i samma system kan du placera deras Application Insights-data i samma resursgrupp.
3. Application Insights är kostnadsfritt upp till en viss telemetrinivå. Du kan tillämpa ett tak vid den volymen för att undvika avgifter. När resursen har skapats kan du ändra ditt val i portalen genom att öppna **Features + pricing** (Funktioner och prissättning), **Data volume management** (Datavolymhantering), **Daily volume cap** (Dagligt volymtak).
4. Klicka på **Registrera** för att fortsätta att konfigurera Application Insights för din webbapp. Telemetri kommer att skickas till [Azure Portal](https://portal.azure.com), både under felsökningen och när du har publicerat din app.
5. Du kan också bara lägga till Application Insights SDK till appen. I det här fallet kommer du att kunna se telemetri i Visual Studio när du felsöker. Du kan senare återvända till den här konfigurationssidan eller vänta tills du har distribuerat appen och [aktivera telemetri vid körtid](app-insights-monitor-performance-live-website-now.md).


## <a name="run"></a> Steg 2: Kör appen
Kör din app med F5. Öppna olika sidor för att skapa viss telemetri.

Du ser hur många händelser som har loggats i Visual Studio.

![Skärmbild av Visual Studio. Knappen Application Insights visas under felsökning.](./media/app-insights-asp-net/54.png)

## <a name="step-3-see-your-telemetry-in-visual-studio-or-application-insights"></a>Steg 3: Se din telemetri i Visual Studio eller Application Insights
Du kan se din telemetri antingen i Visual Studio eller i Application Insights-webbportalen.

Öppna fönstret Application Insights i **Visual Studio**. Klicka antingen på **Application Insights** eller högerklicka på projektet i Solution Explorer, välj **Application Insights** och klicka sedan på alternativet för att **söka i realtidstelemetri**.

I Visual Studio Application Insights-sökfönstret visas vyn **Data från felsökningssessionen** för telemetri som genereras i din app på serversidan. Experimentera med filtren och klicka på en händelse om du vill visa mer information.

![Skärmbild av data från felsökningssessionsvyn i fönstret Application Insights.](./media/app-insights-asp-net/55.png)

> [!NOTE]
> Om du inte ser några data kontrollerar du att tidsintervallet är korrekt och klickar på sökikonen.

[Lär dig mer om Application Insights-verktygen i Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="the-application-insights-web-portal"></a>Application Insights-webbportalen
Du kan även se telemetrin **i Application Insights-webbportalen** såvida du inte väljer att installera enbart SDK:n. Portalen innehåller fler diagram, analysverktyg och instrumentpaneler än Visual Studio.

Öppna Application Insights-resursen. Logga antingen in på [Azure Portal](https://portal.azure.com/) och hitta den där, eller högerklicka på projektet i Visual Studio så kommer du dit.

![Skärmbild av Visual Studio som visar hur du öppnar Application Insights-portalen](./media/app-insights-asp-net/appinsights-04-openPortal.png)

> [!NOTE]
> Om det visas ett åtkomstfel kan du har fler än en uppsättning Microsoft-autentiseringsuppgifter och du kan du vara inloggad med fel uppsättning. Logga ut och logga in igen i portalen.

Portalen öppnas i en vy över telemetrin från appen.
![Skärmbild av Application Insights-översiktssidan](./media/app-insights-asp-net/66.png)

Klicka på en panel eller ett diagram för att se mer information.

### <a name="more-details-in-the-application-insights-web-portal"></a>Mer information finns i Application Insights-webbportalen
Här följer några exempel på hur portalen tillhandahåller mer information åt dig.

* [**Live Metrics Stream**](app-insights-live-stream.md) visar telemetri nästan omedelbart.

    ![Skärmbild av portalen. Klicka på Live Stream från översiktsbladet.](./media/app-insights-asp-net/livestream.png)

    Öppna Live Metrics Stream samtidigt som din app körs, så att de kan anslutas.

    Live Metrics Stream visar bara telemetri i en minut efter att det har skickats. För mer historiska undersökningar använder du Search, Metrics Explorer och Analytics. Det kan ta några minuter innan data visas på dessa platser.

* Under [**Sök**](app-insights-diagnostic-search.md) visar enskilda händelser som begäranden, undantag och sidvyer. Du kan filtrera efter händelsetyp, termmatchning och egenskapsvärden. Klicka på en händelse om du vill visa dess egenskaper och relaterade händelser.

    ![Skärmbild av portalen. Klicka på Sök från översiktsbladet.](./media/app-insights-asp-net/search.png)

 * I utvecklingsläge kan du se många beroendehändelser (AJAX). Dessa är synkroniseringar mellan webbläsaren och serveremulatorn. Klicka på filtret **Beroende** om du vill dölja dem.
* [**Sammansatta mätvärden** ](app-insights-metrics-explorer.md) som begäran och felnivåer visas i diagrammen. Klicka på ett diagram om du vill öppna ett blad med mer information. Klicka på taggen **Redigera** på ett diagram om du vill ange filter och storlek.

    ![Skärmbild av blad med aggregerade mätvärden som är tillgängliga i portalen](./media/app-insights-asp-net/metrics.png)

[Läs mer om hur du använder Application Insights på Azure Portal](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Steg 4: Publicera appen
Publicera din app på din IIS-server eller i Azure. Bevaka [Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream) och se om allt fungerar som det ska.

Du ser din telemetri byggas upp i Application Insights-portalen, där du kan övervaka mått, söka i telemetrin och konfigurera [instrumentpaneler](app-insights-dashboards.md). Du kan också använda det kraftfulla [Analytics-frågespråket](app-insights-analytics.md) för att analysera användning och prestanda eller söka efter specifika händelser.

Du kan också fortsätta att analysera din telemetri i [Visual Studio](app-insights-visual-studio.md) med verktyg som diagnossökning och [trender](app-insights-visual-studio-trends.md).

> [!NOTE]
> Om din app skickar så mycket telemetri att det närmar sig [begränsningsgränserna](app-insights-pricing.md#limits-summary) aktiveras [sampling](app-insights-sampling.md) automatiskt. Sampling minskar mängden telemetri som skickas från din app, samtidigt som korrelerade informationen bevaras i diagnossyfte.
>
>

## <a name="land"></a>Vad används kommandot Lägg till Application Insights till?
Application Insights skickar telemetri från din app till Application Insights-portalen (som finns i Azure).

![Diagram över flödet av telemetri](./media/app-insights-asp-net/01-scheme.png)

Kommandot utför tre saker:

1. Lägger till Application Insights Web SDK NuGet-paketet i projektet. Om du vill visa det i Visual Studio högerklickar du på projektet och väljer **Hantera NuGet-paket**.
2. Skapar en Application Insights-resurs i [Azure-portalen](https://portal.azure.com/). Här visas dina data. Kommandot hämtar den *instrumenteringsnyckel* som identifierar resursen.
3. Infogar instrumenteringsnyckeln i `ApplicationInsights.config` så att SDK kan skicka telemetri till portalen.

Du kan utföra dessa steg manuellt för [ASP.NET 4](app-insights-windows-services.md) eller [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

### <a name="upgrade-to-future-sdk-versions"></a>Uppgradera till framtida SDK-versioner
Om du vill uppgradera till en [ny SDK-version](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases) öppnar du **NuGet-pakethanteraren** igen och filtrerar på installerade paket. Markera **Microsoft.ApplicationInsights.Web** och välj **Uppgradera**.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av den innan du uppgraderar. Sammanfogar sedan dina ändringar i den nya versionen.

## <a name="add-more-telemetry"></a>Lägg till mer telemetri
Följande är andra typer av telemetri som du kan lägga till.
### <a name="dependencies-exceptions-and-performance-counters"></a>Beroenden, undantag och prestandaräknare

[Installera statusövervakare](http://go.microsoft.com/fwlink/?LinkId=506648) på varje IIS-serverdator om du vill hämta ytterligare telemetri om dina webbappar. Om den redan är installerad behöver du inte göra något. (Du kan ha använt statusövervakaren redan för att börja övervaka en app vid körning.)

Med hjälp av statusövervakaren förutom SDK vid byggning får du en mer fullständig uppsättning telemetri som innehåller följande:

* [Prestandaräknare](app-insights-performance-counters.md): Processor, minne, disk och andra prestandaräknare som rör din app.
* [Undantag](app-insights-asp-net-exceptions.md): Mer detaljerad telemetri för vissa undantag.
* [Beroenden](app-insights-asp-net-dependencies.md): Inklusive returvärden.

### <a name="webpages-and-single-page-apps"></a>Webbsidor och enkelsidiga appar
1. [Lägg till JavaScript-kodavsnitt](app-insights-javascript.md) på webbsidorna för att visa data om sidvyer, inläsningstider, webbläsarundantag, prestanda för AJAX-anrop, samt antal användare och sessioner. Dessa visas på bladen Webbläsare och Användning.
2. [Kodanpassade händelser](app-insights-api-custom-events-metrics.md) för att beräkna tid eller mäta användaråtgärder.


### <a name="diagnostic-code"></a>Diagnostikkod
Finns det ett problem? Om du vill infoga kod i din app för att diagnostisera problemet har du flera alternativ:

* [Samla in loggspårningar](app-insights-asp-net-trace-logs.md): Om du redan använder Log4N, NLog eller System.Diagnostics.Trace för att logga spårningshändelser kan utdata skickas till Application Insights. Du kan knyta dessa utdata till begäranden, söka igenom dem och analysera dem.
* [Anpassade händelser och mätvärden](app-insights-api-custom-events-metrics.md): Använd TrackEvent() och TrackMetric() i server- eller webbsidekoden.
* [Tagga telemetri med ytterligare egenskaper](app-insights-api-filtering-sampling.md#add-properties).

Använd [Sök](app-insights-diagnostic-search.md) för att söka efter och korrelera specifika händelser och [Analys](app-insights-analytics.md) att utföra mer kraftfulla frågor.

## <a name="alerts"></a>Aviseringar
Var först med att veta om din app har problem.

* [Tillgänglighetstester](app-insights-monitor-web-app-availability.md): Skapa tester som kan användas för att kontrollera att webbplatsen visas på webben.
* [Smart diagnostik](app-insights-proactive-diagnostics.md): De här testerna körs automatiskt, så du behöver inte göra något för att konfigurera dem. De berättar om din app har ett ovanligt antal misslyckade begäranden.
* [Måttaviseringar](app-insights-alerts.md): Används för att varna dig om ett mått överskrider ett tröskelvärde. Du kan ställa in dem för anpassade mätningar som du kodar i din app.

Som standard skickas aviseringsmeddelanden till ägaren av Azure-prenumerationen.

![Skärmbild av exempel på aviseringar via e-post](./media/app-insights-asp-net/alert-email.png)

## <a name="version-and-release-tracking"></a>Spårning av versionen och utgåva
Om du vill kunna spåra programversionen, se till att `buildinfo.config` genereras av Microsoft Build Engine-processen. I filen .csproj lägger du till:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

När Application Insights-webbmodulen har fått versionsinformationen läggs **programversionen** automatiskt till som en egenskap för alla telemetriobjekt. Det gör att du kan filtrera baserat på version när du utför [diagnostiksökningar](app-insights-diagnostic-search.md) eller när du [undersöker mätvärden](app-insights-metrics-explorer.md).

Observera dock att build-versionsnumret endast genereras av Microsoft Build Engine, och inte av utvecklarversionen i Visual Studio.

### <a name="release-annotations"></a>Versionsanteckningar
Om du använder Visual Studio Team Services, kan du [få en anteckningsmarkör](app-insights-annotations.md) tillagd i diagrammen när du släpper en ny version. Följande bild visar hur markeringen visas.

![Skärmbild av exempel på versionsanteckning i ett diagram](./media/app-insights-asp-net/release-annotation.png)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg
**[Arbeta med Application Insights i Visual Studio](app-insights-visual-studio.md)**<br/>Innehåller information om att felsöka med telemetri, köra diagnostiksökningar och gå igenom koden.

**[Arbeta med Application Insights-portalen](app-insights-dashboards.md)**<br/> Innehåller information om instrumentpaneler, kraftfulla verktyg för diagnostik och analys, aviseringar, realtidsmappning av beroenden för din app och telemetriexport.

