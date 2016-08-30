<properties
    pageTitle="Komma igång med Visual Studio Application Insights | Microsoft Azure"
    description="Analysera användningen, tillgängligheten och prestanda i ditt lokala program eller din Microsoft Azure-webbapp med Visual Studio Application Insights."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="03/31/2016"
    ms.author="awills"/>

# Komma igång med Visual Studio Application Insights

*Application Insights finns endast som förhandsversion.*

Identifiera fel, lös problem och förbättra dina program kontinuerligt. Diagnostisera snabbt problem i ditt liveprogram. Förstå vad användarna gör med det.

Konfigurationen är mycket enkelt och du ser resultaten inom några minuter.

Vi stöder för närvarande iOS-, Android- och Windows-appar, J2EE- och ASP.NET-webbprogram och WCF-tjänster. Webbappar kan köras i Azure eller på dina egna lokala servrar. Vårt JavaScript SDK kan köras på alla typer av webbsidor.

[Titta på introduktionsanimeringen](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## Kom igång

Börja med valfri kombination, i valfri ordning, av startpunkterna som visas i följande diagram. Följ den väg som passar dig.

När du använder Application Insights läggs ett SDK till i din app, som skickar telemetri till [Azure-portalen](https://portal.azure.com). Det finns olika SDK:er för de olika kombinationerna av plattformar, språk och IDE som stöds.

Du behöver ett konto i [Microsoft Azure](http://azure.com). Du kanske redan har åtkomst till ett gruppkonto via din organisation eller så kanske du vill skaffa ett ”betala per användning”-konto. Application Insights har en kostnadsfri nivå, så du behöver inte betala förrän din app har blivit populär. Gå till [sidan med priser](https://azure.microsoft.com/pricing/details/application-insights/).

Vad du vill ha | Vad du gör | Vad du får
---|---|---
 <a href="app-insights-asp-net.md">![ASP.NET](./media/app-insights-get-started/appinsights-gs-i-01-perf.png)</a> | <a href="app-insights-asp-net.md">Lägg till Application Insights SDK till ditt webbprojekt</a> <br/> ![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-asp-net.md">![Övervakning av prestanda och användning](./media/app-insights-get-started/appinsights-gs-r-01-perf.png)</a>
<a href="app-insights-monitor-performance-live-website-now.md">![ASP.NET-webbplats redan live](./media/app-insights-get-started/appinsights-gs-i-04-red2.png)</a><br/><a href="app-insights-monitor-performance-live-website-now.md">![Övervakning av beroenden och prestanda](./media/app-insights-get-started/appinsights-gs-i-03-red.png)</a>|<a href="app-insights-monitor-performance-live-website-now.md">Installera Status Monitor på IIS-servern</a> <br/> ![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-monitor-performance-live-website-now.md">![Övervakning av ASP.NET-beroenden](./media/app-insights-get-started/appinsights-gs-r-03-red.png)</a>
<a href="insights-perf-analytics.md">![Azure-webbapp eller virtuell dator](./media/app-insights-get-started/appinsights-gs-i-10-azure.png)</a>|<a href="insights-perf-analytics.md">Aktivera Insights i Azure-webbappen eller en virtuell dator</a> <br/> ![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="insights-perf-analytics.md">![Övervakning av beroenden och prestanda](./media/app-insights-get-started/appinsights-gs-r-03-red.png)</a>
<a href="app-insights-java-get-started.md">![Java](./media/app-insights-get-started/appinsights-gs-i-11-java.png)</a>|<a href="app-insights-java-get-started.md">Lägg till SDK i ditt Java-projekt</a><br/>![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-java-get-started.md">![Övervakning av prestanda och användning](./media/app-insights-get-started/appinsights-gs-r-10-java.png)</a>
<a href="app-insights-web-track-usage.md">![JavaScript](./media/app-insights-get-started/appinsights-gs-i-02-usage.png)</a>|<a href="app-insights-web-track-usage.md">Infoga Application Insights-skriptet i dina webbsidor</a><br/>![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-web-track-usage.md">![sidvisningar och webbläsarprestanda](./media/app-insights-get-started/appinsights-gs-r-02-usage.png)</a>
<a href="app-insights-monitor-web-app-availability.md">![Tillgänglighet](./media/app-insights-get-started/appinsights-gs-i-05-avail.png)</a>|<a href="app-insights-monitor-web-app-availability.md">Skapa webbtester</a><br/>![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-monitor-web-app-availability.md">![Tillgänglighet](./media/app-insights-get-started/appinsights-gs-r-05-avail.png)</a>
<a href="app-insights-platforms.md">![iOS-, Android- och Windows-enheter](./media/app-insights-get-started/appinsights-gs-i-07-device.png)</a>|<a href="http://hockeyapp.net">Använd HockeyApp</a><br/>![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="http://hockeyapp.net">![Krasch- och användningsdata](./media/app-insights-get-started/appinsights-gs-r-06-device.png)</a>

## Support och feedback


* Frågor och problem:
 * [Felsökning][qna]
 * [MSDN-forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
 * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Buggar:
 * [Anslut](https://connect.microsoft.com/VisualStudio/Feedback/LoadSubmitFeedbackForm?FormID=6076)
* Förslag:
 * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* Kodexempel
 * [Kodexempel](app-insights-code-samples.md)



## <a name="video"></a>Videoklipp


> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]



<!--Link references-->

[qna]: app-insights-troubleshoot-faq.md



<!--HONumber=jun16_HO2-->


