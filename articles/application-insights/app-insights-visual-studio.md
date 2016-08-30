<properties 
    pageTitle="Arbeta med Application Insights i Visual Studio" 
    description="Prestandaanalys och diagnostik vid felsökning och i produktion." 
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


# Arbeta med Application Insights i Visual Studio

I Visual Studio (2015 och senare) kan du analysera prestanda och diagnostisera problem både när du felsöker och i produktion med hjälp av telemetri från [Visual Studio Application Insights](app-insights-overview.md).

Om du inte har [installerat Application Insights i din app](app-insights-asp-net.md) än så gör du det nu.

## <a name="run"></a> Felsöka ditt projekt

Kör ditt program med F5 och prova det: öppna olika sidor för att generera telemetri.

I Visual Studio ser du antalet händelser som har loggats.

![I Visual Studio visas knappen Application Insights när du felsöker.](./media/app-insights-visual-studio/appinsights-09eventcount.png)

Klicka på knappen om du vill öppna en diagnostiksökning. 



## Diagnostiksökning

I sökfönstret visar händelser som har loggats. (Om du är inloggad i Azure när du konfigurerar Application Insights kan du söka efter samma händelser på portalen.)

![Högerklicka på projektet och välj Application Insights, Sök](./media/app-insights-visual-studio/34.png)

Fritextsökningen fungerar för alla fält i händelserna. Du kan till exempel söka efter en del av URL-adressen för en sida, efter värdet för en egenskap, t.ex. klientens stad, eller efter specifika ord i en spårningslogg.

Klicka på en händelse om du vill visa händelsens detaljerade egenskaper.

Du kan också öppna fliken Relaterade objekt för att diagnostisera misslyckade begäranden eller undantag.


![](./media/app-insights-visual-studio/41.png)



## Diagnostics Hub

Diagnostics Hub (i Visual Studio 2015 eller senare) visar Application Insights-servertelemetrin när den genereras. Detta fungerar även om du valde att bara installera SDK, utan att ansluta det till en resurs på Azure-portalen.

![Öppna fönstret Diagnostikverktyg och granska Application Insights-händelserna.](./media/app-insights-visual-studio/31.png)


## Undantag

Om du har [konfigurerat undantagsövervakning](app-insights-asp-net-exceptions.md) visas undantagsrapporter i sökfönstret. 

Klicka på ett undantag för att visa en stackspårning. Om koden för appen är öppen i Visual Studio kan du klicka dig igenom från stackspårningen till relevant kodrad.


![](./media/app-insights-visual-studio/17.png)


## Lokal övervakning



(Från och med Visual Studio 2015 Update 2) Om du inte har konfigurerat SDK för att skicka telemetri till Application Insights-portalen (så att det inte finns någon instrumentationsnyckel i ApplicationInsights.config) visar diagnostikfönstret telemetri från din senaste felsökningssession. 

Detta är lämpligt om du redan har publicerat en tidigare version av din app. Du vill undvika att telemetri från dina felsökningssessioner blandas med telemetrin på Application Insights-portalen från den publicerade appen.

Det är också användbart om du har [anpassad telemetri](app-insights-api-custom-events-metrics.md) som du vill felsöka innan du skickar telemetri till portalen.


* *Först konfigurerade jag Application Insights att skicka telemetrin till portalen. Men nu vill jag bara visa telemetrin i Visual Studio.*

 * I sökfönstrets inställningar finns det ett alternativ för att söka i lokal diagnostik även om din app skickar telemetri till portalen.
 * Om du inte vill att telemetri ska skickas till portalen längre kommenterar du bort raden `<instrumentationkey>...` i ApplicationInsights.config. När du vill skicka telemetri till portalen igen avkommenterar du raden.





## Så här uppgraderar du till framtida SDK-versioner

Om du vill uppgradera till en [ny SDK-version](app-insights-release-notes-dotnet.md) öppnar du NuGet-pakethanteraren igen och filtrerar på installerade paket. Markera Microsoft.ApplicationInsights.Web och välj Uppgradera.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av filen innan du uppgraderar och sammanfogar sedan dina ändringar i den nya versionen.



## Nästa steg

||
|---|---
|**[Lägga till mer information](app-insights-asp-net-more.md)**<br/>Övervaka användning, tillgänglighet, beroenden och undantag. Integrera spårningar från loggningsramverk. Skriv anpassad telemetri. | ![Visual Studio](./media/app-insights-asp-net/64.png)
|**[Arbeta med Application Insights-portalen](app-insights-dashboards.md)**<br/>Instrumentpaneler, kraftfulla verktyg för diagnostik och analys, aviseringar, live-mappning över beroenden för din app och telemetriexport. |![Visual Studio](./media/app-insights-asp-net/62.png)


 



<!--HONumber=jun16_HO2-->


