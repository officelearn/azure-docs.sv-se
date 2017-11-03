---
title: "Övervaka hälsotillstånd och användning med Application Insights för din app"
description: "Kom igång med Application Insights. Analysera användning, tillgänglighet och prestanda för din lokala eller Microsoft Azure-program."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: mbullwin
ms.openlocfilehash: 32000f5a85c84913aa820df00f1bb7f877bf037f
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-performance-in-web-applications"></a>Övervaka prestanda i webbprogram


Kontrollera att programmet fungerar bra och lär dig snabbt eventuella fel. [Application Insights] [ start] information om eventuella prestandaproblem och undantag, och hjälper dig att hitta och diagnostisera orsaken.

Application Insights kan övervaka Java- och ASP.NET-webbprogram och tjänster, WCF-tjänster. De kan vara lokalt, på virtuella datorer eller som Microsoft Azure-webbplatser. 

På klientsidan ta Programinsikter telemetri från webbsidor och en mängd olika enheter, inklusive iOS, Android och Windows Store-appar.

>[!Note]
> Vi har gjort en ny miljö för att hitta långsamt utför sidor i ditt webbprogram. Om du inte har åtkomst till den, aktivera den genom att konfigurera alternativen preview med den [Preview bladet](app-insights-previews.md). Läs mer om den nya upplevelsen i [hitta och åtgärda flaskhalsar i interaktiva prestanda undersökningen](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation).

## <a name="setup"></a>Konfigurera övervakning av programprestanda
Om du inte har lagt till Application Insights i projektet (om det inte har ApplicationInsights.config), väljer du något av följande sätt att komma igång:

* [ASP.NET-webbprogram](app-insights-asp-net.md)
  * [Lägg till undantagsövervakning](app-insights-asp-net-exceptions.md)
  * [Lägg till beroende övervakning](app-insights-monitor-performance-live-website-now.md)
* [J2EE-webbprogram](app-insights-java-get-started.md)
  * [Lägg till beroende övervakning](app-insights-java-agent.md)

## <a name="view"></a>Utforska prestandamått
I [Azure-portalen](https://portal.azure.com), bläddra till Application Insights-resursen som du har skapat för ditt program. Översikt över bladet visar grundläggande prestandadata:

Klicka på diagram finns mer information och visa resultat för en längre period. Exempel på panelen begäranden och välj sedan ett tidsintervall:

![Klicka här för att mer data och välj ett tidsintervall](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Klicka på ett diagram om du vill välja vilka mått som visas, eller lägga till ett nytt diagram och välja dess mått:

![Klicka på ett diagram om du vill välja mått](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Avmarkera alla mätvärden** Visa fullständig urval som är tillgänglig. Mätvärdena är indelade i grupper. När alla medlemmar i en grupp väljs, visas bara andra medlemmar av gruppen.

## <a name="metrics"></a>Vad betyder det alla? Prestanda paneler och rapporter
Det finns olika prestandamått som du kan hämta. Vi börjar med de som visas som standard på bladet program.

### <a name="requests"></a>Begäranden
Antal HTTP-begäranden tas emot i en angiven period. Jämför detta med resultat på andra rapporter för att se hur din app fungerar som belastningen varierar.

HTTP-begäranden som innehåller alla begäranden om sidor, data och avbildningar som GET eller POST.

Klicka på rutan för att få fram för specifika URL: er.

### <a name="average-response-time"></a>Genomsnittlig svarstid
Mäter tiden mellan en webbegäran att ange ditt program och svaret returneras.

Punkterna visar det genomsnittliga glidande dem. Om det finns en mängd begäranden, kan det finnas några som avviker från medelvärdet utan en uppenbara belastning eller sjunker i diagrammet.

Leta efter ovanliga toppar. I allmänhet förvänta svarstid för att öka med en ökning av begäranden. Om ökar oproportionerligt att din app träffa en resursgräns t.ex CPU eller kapaciteten för en tjänst som används.

Klicka på panelen om du vill hämta gånger för specifika URL: er.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Långsammaste begäranden
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Visar vilka begäranden kan behöva prestandajustering.

### <a name="failed-requests"></a>Misslyckade begäranden
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Antalet begäranden som utlöste undantagsfel utan felhantering.

Klicka på panelen om du vill se information om specifika problem och välj en enskild begäran kan se dess detaljer. 

Ett representativt urval fel sparas för enskilda kontroll.

### <a name="other-metrics"></a>Andra mått
Om du vill se vad anges andra mått som du kan visa, klicka på ett diagram och sedan avmarkera alla mått att visa hela tillgängliga. Klicka på (i) om du vill se varje mått definition.

![Avmarkera alla mått för att se hela uppsättningen](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Om du väljer alla mått inaktiveras andra som inte får finnas i samma schema.

## <a name="set-alerts"></a>Ange aviseringar
Lägga till en avisering om du vill meddelas via e-post med ovanliga värden för alla mått. Du kan välja att skicka e-postmeddelandet till kontoadministratörer eller till specifika e-postadresser.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Ange resursen innan de andra egenskaperna. Välj inte webtest resurser om du vill ställa in varningar på prestanda eller användningsområden mätvärden.

Var noga med att känna till de enheter där du uppmanas att ange ett tröskelvärde.

*Knappen Lägg till avisering visas inte.* -Är detta en grupp som du har skrivskyddad åtkomst till kontot? Kontakta kontoadministratören.

## <a name="diagnosis"></a>Diagnostisera problem
Här följer några tips för att hitta och diagnostisera prestandaproblem:

* Ställ in [webbtester] [ availability] om webbplatsen kraschar eller svarar långsamt eller felaktigt. 
* Jämför antalet begäranden med andra mått för att se om misslyckade eller långsamma svar är relaterade till att läsa in.
* [Infoga och Sök trace-satser] [ diagnostic] i koden för att identifiera problem.
* Övervaka ditt webbprogram i åtgärden med [direktsänd dataström med mått][livestream].
* Spara tillståndet för .net-program med [ögonblicksbild felsökare][snapshot].

>[!Note]
> Vi håller övergång Application Insights prestanda undersökning till en interaktiv upplevelse i helskärmsläge. Följande dokumentation omfattar den nya upplevelsen först och går igenom den tidigare erfarenhet om du fortfarande behöver åtkomst till den, medan den fortfarande är tillgängliga i hela övergången.

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-full-screen-performance-investigation"></a>Hitta och åtgärda flaskhalsar med en interaktiv helskärmsläge prestanda undersökning

Du kan använda den nya Application Insights interaktiva prestanda undersökningen för att granska långsamt utför åtgärder i ditt webbprogram. Du snabbt kan välja en specifik långsamt och använda [Profiler](app-insights-profiler.md) till rot orsaka långsam operations ned koden. Med hjälp av nya varaktigheten distributionen visas för den valda åtgärden kan du snabbt en överblick över utvärdera hur allvarligt är upplevelsen för kunderna. I själva verket för varje långsam åtgärd ser du hur många användarinteraktioner har påverkas. I exemplet nedan har vi valt att ta en närmare titt på upplevelsen för åtgärden hämta kunder/detaljer. Vi kan se att det finns tre toppar i distributionen varaktighet. Längst till vänster topp är cirka 400ms och representerar bra responsiv upplevelse. Mellersta topp är cirka 1.2s och representerar en medelmåttiga upplevelse. Slutligen ha 3.6s vi en annan små topp som representerar 99th percentil upplevelse, vilket kan medföra våra kunder att lämna nöjd. Att upplevelse är tio gånger långsammare än bra upplevelsen för samma åtgärd. 

![Hämta kunder/information tre varaktighet toppar](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Vi kan välja en större tidsintervallet för att få en bättre uppfattning av erfarenheterna från användare för den här åtgärden. Vi kan sedan också begränsa tidpunkt på ett specifikt tidsfönster där åtgärden var särskilt långsam. I följande exempel har vi ändrats från standardvärdet 24 timmar tidsintervallet till 7 dagar tidsintervallet och sedan zoomat in 9:47 till 12:47 tidsfönstret mellan TIS 12 och ons 13. Observera att både varaktighet-distribution och antalet prov och profiler-spårningar har uppdaterats till höger.

![Hämta kunder/information tre varaktighet toppar i 7 dagar intervall med ett tidsfönster](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Om du vill begränsa i på erfarenheterna från långsamma vi bredvid Zooma in i varaktighet som ligger mellan 95th och 99th percentil. Dessa motsvarar användarinteraktioner som var särskilt långsam % 4.

![Hämta kunder/information tre varaktighet toppar i 7 dagar intervall med ett tidsfönster](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Vi kan nu antingen titt på representativt exempel genom att klicka på knappen exempel eller på representativt profiler-spårningar genom att klicka på knappen Profiler-spårningar. I det här exemplet finns det fyra spårningar som har samlats in för hämta kunder/detaljer i varaktighet för fönster och intervallet av intresse.

Ibland problemet inte koden, men i ett beroende du koden anropar. Du kan växla till fliken beroenden i prioritering prestandavyn ska undersökas sådan långsam beroenden. Observera att prestandavyn är trender medelvärden som standard, men du vill titta på 95: e percentilen (eller 99th, om du övervakar en mycket mogen tjänst). I följande exempel har vi fokuserar på långsam Azure BLOB beroendet, där vi kallar PUT fabrikamaccount. Bra upplevelser kluster runt 40ms, medan de långsamma anrop till samma beroendet är tre gånger långsammare, klustring runt 120ms. Det tar inte många av dessa anrop att lägga till respektive åtgärden avsevärt långsammare. Detaljer om representativa prover och profiler-spårningar, precis som med fliken åtgärder.

![Hämta kunder/information tre varaktighet toppar i 7 dagar intervall med ett tidsfönster](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

En annan mycket kraftfull funktion som är ny för interaktiva helskärmsläge prestanda undersökningen är integreringen med insikter. Application Insights kan identifiera och ytan som insikter svarstider regressioner samt hjälp du identifiera gemensamma egenskaper i exempeldata som du har valt att fokusera på. Det bästa sättet att titta på alla insikter som är tillgängliga är att växla till ett tidsintervall för 30 dagar och välj sedan övergripande få insikter mellan alla åtgärder för den senaste månaden.

![Hämta kunder/information tre varaktighet toppar i 7 dagar intervall med ett tidsfönster](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)

Application Insights i den nya prestanda prioritering vyn bokstavligt hjälpa dig att hitta barren i höstack som resultera i sämre upplevelser för användarnas Web app.

## <a name="deprecated-find-and-fix-performance-bottlenecks-with-a-narrow-bladed-legacy-performance-investigation"></a>Föråldrad: Hitta och åtgärda flaskhalsar med en undersökning smala bladed äldre prestanda

Du kan använda äldre Application Insights bladed prestanda undersökningen för att hitta områden av ditt webbprogram som saktar ned prestandan. Du kan hitta vissa sidor som saktar ned och använder den [Profiler](app-insights-profiler.md) att spåra orsaken till problemen ned koden. 

### <a name="create-a-list-of-slow-performing-pages"></a>Skapa en lista över långsamma prestanda sidor 

Det första steget för att söka efter prestandaproblem är att hämta en lista över långsamma svarar sidor. Skärmbilden nedan visar hur du använder bladet prestanda för att hämta en lista över möjliga sidor för att undersöka vidare. Du kan snabbt se från den här sidan att det var en nedgång svarstiden för appen på cirka 6:00 PM och igen på cirka 10 PM. Du kan också se att hämta kundinformation /-åtgärden har vissa långvariga åtgärder med en median svarstid 507.05 millisekunder. 

![Application Insights interaktiva prestanda](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>Detaljnivån på specifika sidor

När du har en ögonblicksbild av appens prestanda kan få du mer information på specifika åtgärder för långsamma prestanda. Klicka på alla åtgärder i listan visas information som visas nedan. Du kan se om prestanda var baserat på ett beroende i diagrammet. Du kan också se hur många användare haft olika svarstider. 

![Application Insights operations bladet](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>Detaljnivån för en viss tidsperiod

När du har identifierat en punkt i tid att undersöka, detaljnivån även titta på åtgärderna som kan ha orsakat prestanda sidåtergivningen. När du klickar på en viss punkt i tiden få information om sidan som visas nedan. I exemplet nedan du kan se de åtgärder som anges för en viss tidsperiod tillsammans med svarskoder server och varaktighet för åtgärden. Du kan också ha URL: en för att öppna en TFS-arbetsobjekt om du behöver skicka denna information till Utvecklingsteamet.

![Tidsintervallet för Application Insights](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>Detaljnivån med en specifik åtgärd

När du har identifierat en punkt i tid att undersöka, detaljnivån även titta på åtgärderna som kan ha orsakat prestanda sidåtergivningen. Klicka på en åtgärd från listan för att visa detaljer om åtgärden som visas nedan. I det här exemplet ser du att åtgärden misslyckades och Application Insights tillhandahåller information om undantaget inträffade i programmet. Igen, kan du enkelt skapa en TFS-arbetsobjekt från det här bladet.

![Application Insights åtgärden bladet](./media/app-insights-web-monitor-performance/performance3.png)


## <a name="next"></a>Nästa steg
[Webbtester] [ availability] -har webbförfrågningar som skickas till ditt program med jämna mellanrum från hela världen.

[Fånga och söka diagnostikspår] [ diagnostic] – Infoga spårning av anrop och gå igenom resultat för att identifiera problem.

[Spårning av användning] [ usage] -ta reda på hur personer använder ditt program.

[Felsökning av] [ qna] - och frågor och svar



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md



