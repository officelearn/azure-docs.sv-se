---
title: Vad är Azure Application Insights? | Microsoft Docs
description: Application Performance Management och användningsspårning av ditt live-webbprogram.  Identifiera, hantera och diagnostisera problem och förstå hur din app används.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 09/06/2018
ms.author: mbullwin
ms.openlocfilehash: aa9c02e069700ab236c4b9fc78482450b5911e93
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983881"
---
# <a name="what-is-application-insights"></a>Vad är Application Insights?
Application Insights är en utökningsbar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Den identifierar automatiskt prestandaavvikelser. Den inkluderar kraftfulla analysverktyg för att hjälpa dig diagnosticera problem och förstå vad användare faktiskt gör med din app.  Den hjälper dig att kontinuerligt förbättra prestanda och användbarhet. Den fungerar för appar på en rad olika plattformar, däribland .NET, Node.js och J2EE som finns lokalt eller i molnet. Den integrerar med din DevOps-process och kan användas med en rad olika utvecklingsverktyg. Den kan övervaka och analysera telemetri från mobilappar genom integrering med Visual Studio App Center.

[Titta på introduktionsanimeringen](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## <a name="how-does-application-insights-work"></a>Hur fungerar Application Insights?
Du installerar ett litet instrumentationspaket i ditt program och konfigurerar en Application Insights-resurs på Microsoft Azure Portal. Instrumentationen övervakar din app och skickar telemetridata till portalen. (Programmet kan köras överallt – det behöver inte finnas i Azure.)

Förutom att instrumentera webbtjänstprogrammet kan du även instrumentera bakgrundskomponenter och JavaScript-koden på själva webbsidorna. 

![Application Insights-instrumentationen i appen skickar telemetri till Application Insights-resursen.](./media/app-insights-overview/01-scheme.png)

Du kan också hämta telemetri från värdmiljöerna, t.ex. prestandaräknare, Azure-diagnostik eller Docker-loggar. Du kan även konfigurera webbtester som regelbundet skickar syntetiska förfrågningar till din webbtjänst.

Alla dessa telemetriströmmar är integrerade på Azure Portal, där du kan använda kraftfulla analys- och sökverktyg med dina rådata.


### <a name="whats-the-overhead"></a>Hur påverkas prestanda?
Effekten på din apps prestanda är mycket liten. Anropsspårning är icke-blockerande, och grupperas och skickas i en separat tråd.

## <a name="what-does-application-insights-monitor"></a>Vad övervakar Application Insights?

Application Insights är avsett för utvecklingsteamet och gör det lättare att förstå hur appen fungerar och hur den används. Tjänsten övervakar:

* **Begärandefrekvens, svarstider och felfrekvens** – Ta reda på vilka sidor som är mest populära, vid vilka tidpunkter på dagen och var dina användare finns. Se vilka sidor som fungerar bäst. Om svarstiden och felfrekvensen är hög när det finns många begäranden kan det bero på ett resurstilldelningsproblem. 
* **Beroendefrekvens, svarstider och felfrekvens** – Ta reda på om externa tjänster gör systemet långsammare.
* **Undantag** – Analysera aggregerad statistik, eller välj specifika instanser och visa detaljerad information om stackspårningen och relaterade begäranden. Både server- och webbläsarundantag rapporteras.
* **Sidvyer och inläsningsprestanda** – Rapporteras av användarnas webbläsare.
* **AJAX-anrop** från webbsidor – frekvens, svarstider och felfrekvens.
* **Antal användare och sessioner**.
* **Prestandaräknare** från dina Windows- eller Linux-serverdatorer, till exempel processor, minne och nätverksanvändning. 
* **Värddiagnostik** från Docker eller Azure. 
* **Diagnostikspårningsloggar** från din app – så att du kan jämföra spårningshändelser med begäranden.
* **Anpassade händelser och mätvärden** som du skriver själv i klient- eller serverkoden för att spåra affärshändelser, t.ex. sålda artiklar eller vunna spel.

## <a name="where-do-i-see-my-telemetry"></a>Var ser jag min telemetri?

Du kan utforska dina data på många sätt. Läs dessa artiklar:

|  |  |
| --- | --- |
| [**Smart identifiering och manuella aviseringar**](app-insights-proactive-diagnostics.md)<br/>Automatiska aviseringar är anpassade efter appens normala telemetrimönster och visas vid händelser som avviker från det vanliga mönstret. Du kan också [ställa in aviseringar](app-insights-alerts.md) på särskilda nivåer med anpassade måttvärden eller standardmått. |![Aviseringsexempel](./media/app-insights-overview/alerts-tn.png) |
| [**Programkarta**](app-insights-app-map.md)<br/>Komponenterna i din app, med viktiga mätvärden och aviseringar. |![Programkarta](./media/app-insights-overview/appmap-tn.png)  |
| [**Profilerare**](app-insights-profiler.md)<br/>Granska körningsprofilerna för exempelbegäranden. |![Profilerare](./media/app-insights-overview/profiler.png) |
| [**Användningsanalys**](app-insights-usage-overview.md)<br/>Analysera användarsegment och kvarhållning.|![Kvarhållningsverktyg](./media/app-insights-overview/retention.png) |
| [**Diagnostiksökning efter instansdata**](app-insights-diagnostic-search.md)<br/>Sök efter och filtrera händelser, till exempel begäranden, undantag, beroendeanrop, loggspårningar och sidvyer.  |![Telemetrisökning](./media/app-insights-overview/search-tn.png) |
| [**Metrics Explorer för aggregerade data**](app-insights-metrics-explorer.md)<br/>Utforska, filtrera och segmentera aggregerade data, till exempel begärande-, fel- och undantagsfrekvens, svarstider och sidinläsningstider. |![Mått](./media/app-insights-overview/metrics-tn.png) |
| [**Instrumentpaneler**](app-insights-dashboards.md#dashboards)<br/>Kombinera data från flera resurser och dela med andra. Perfekt för program med flera komponenter och för kontinuerlig visning i teamutrymmet. |![Exempel på instrumentpaneler](./media/app-insights-overview/dashboard-tn.png) |
| [**Live-ström med mätvärden**](app-insights-live-stream.md)<br/>När du distribuerar en ny version kan du titta på dessa prestandaindikatorer som visas i realtid för att kontrollera att allt fungerar som förväntat. |![Exempel på live-mätvärden](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analytics**](app-insights-analytics.md)<br/>Besvara svåra frågor om appens prestanda och användning med hjälp av det här kraftfulla frågespråket. |![Analytics-exempel](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](app-insights-visual-studio.md)<br/>Visa prestandadata i koden. Gå till kod från stackspårningar.|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Felsökning av ögonblicksbild**](app-insights-snapshot-debugger.md)<br/>Felsök ögonblicksbilder från program som körs med parametervärden.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](app-insights-export-power-bi.md)<br/>Integrera användningsmätvärden med annan Business Intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Skriv kod för att köra frågor mot dina mätvärden och rådata.| ![REST-API](./media/app-insights-overview/rest-tn.png) |
| [**Löpande export**](app-insights-export-telemetry.md)<br/>Massexportera rådata till lagring så fort de anländer. |![Exportera](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Hur använder jag Application Insights?

### <a name="monitor"></a>Övervaka
Installera Application Insights i din app, konfigurera [webbtester för tillgänglighet](app-insights-monitor-web-app-availability.md) och:

* Konfigurera en [instrumentpanel](app-insights-dashboards.md) för ditt teamutrymme för att hålla ett öga på belastning, tillgänglighet och prestanda för dina beroenden, sidinläsningar och AJAX-anrop.
* Identifiera de begäranden som är långsammast och som misslyckas oftast.
* Titta på [Live Stream](app-insights-live-stream.md) när du distribuerar en ny version så att du genast kan upptäcka en eventuell försämring.

### <a name="detect-diagnose"></a>Identifiera och diagnostisera
När du får en avisering eller identifierar ett problem:

* Utvärdera hur många användare som påverkas.
* Korrelera fel med undantag, beroendeanrop och spårningar.
* Undersök profilerare, ögonblicksbilder, stackdumpar och spårningsloggar.

### <a name="build-measure-learn"></a>Bygg, mät och lär
[Mät effektiviteten](app-insights-usage-overview.md) i varje ny funktion som du distribuerar.

* Mät hur kunder använder nya användargränssnitts- eller företagsfunktioner.
* Skriv in anpassad telemetri i din kod.
* Basera nästa utvecklingscykel på fakta från din telemetri.

## <a name="get-started"></a>Kom igång
Application Insights är en av de många tjänsterna som finns i Microsoft Azure, och telemetri skickas dit för analys och visning. Innan du gör något annat behöver du alltså en prenumeration på [Microsoft Azure](http://azure.com). Registreringen är gratis och om du väljer den grundläggande [prisplanen](https://azure.microsoft.com/pricing/details/application-insights/) för Application Insights betalar du inget förrän programmet har växt så pass mycket att det har en betydande användning. Om din organisation redan har en prenumeration kan du lägga till ditt Microsoft-konto till den.

Det finns flera sätt att komma igång. Börja på det sätt som passar dig bäst. Du kan fortsätta med de andra senare.

* **Vid körning: instrumentera webbappen på servern.** Undviker uppdateringar av koden. Du behöver administratörsåtkomst till servern.
  * [**IIS lokalt eller på en virtuell dator**](app-insights-monitor-performance-live-website-now.md)
  * [**Azure-webbapp eller virtuell dator**](app-insights-monitor-performance-live-website-now.md)
  * [**J2EE**](app-insights-java-live.md)
* **Vid utveckling: lägg till Application Insights i din kod.** Gör att du kan skriva anpassad telemetri och instrumentera serverdels- och skrivbordsappar.
  * [Visual Studio](app-insights-asp-net.md) 2013 uppdatering 2 eller senare.
  * [Java](app-insights-java-get-started.md)
  * [Node.js](app-insights-nodejs.md)
  * [Andra plattformar](app-insights-platforms.md)
* **[Instrumentera dina webbsidor](app-insights-javascript.md)** för sidvisning, AJAX och annan telemetri på klientsidan.
* **[Analysera mobilappsanvändning](app-insights-mobile-center-quickstart.md)** genom integrering med Visual Studio App Center.
* **[Tillgänglighetstester](app-insights-monitor-web-app-availability.md)** –pinga din webbplats regelbundet från våra servrar.


## <a name="next-steps"></a>Nästa steg
Kom igång under körningsfasen med:

* [IIS-server](app-insights-monitor-performance-live-website-now.md)
* [J2EE-server](app-insights-java-live.md)

Kom igång under utvecklingsfasen med:

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.js](app-insights-nodejs.md)

## <a name="support-and-feedback"></a>Support och feedback
* Frågor och problem:
  * [Felsökning][qna]
  * [MSDN-forum](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Dina förslag:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blogg:
  * [Application Insights-blogg](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>Videoklipp

[![Animerad introduktion](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

<!--Link references-->

[android]: app-insights-mobile-center-quickstart.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: app-insights-mobile-center-quickstart.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
