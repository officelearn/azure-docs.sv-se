---
title: Övervaka appens hälsa och användning med Application Insights
description: Kom igång med Application Insights. Analysera användning, tillgänglighet och prestanda för dina lokala eller Microsoft Azure program.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: f84e1065f93fc2cca950f12d5df537b931faac8d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987152"
---
# <a name="monitor-performance-in-web-applications"></a>Övervaka prestanda i webbprogram


Kontrol lera att programmet fungerar bra och ta reda på mer om eventuella fel. [Application Insights][start] meddelar dig om prestanda problem och undantag, och hjälper dig att hitta och diagnostisera rotor saken.

Application Insights kan övervaka både Java-och ASP.NET-webbprogram och-tjänster, WCF-tjänster. De kan finnas lokalt, på virtuella datorer eller som Microsoft Azure webbplatser. 

På klient sidan kan Application Insights ta telemetri från webb sidor och en rad olika enheter, inklusive iOS-, Android-och Windows Store-appar.

## <a name="set-up-performance-monitoring"></a><a name="setup"></a>Konfigurera prestanda övervakning
Om du ännu inte har lagt till Application Insights i projektet (det vill säga om det inte har ApplicationInsights.config) väljer du något av följande sätt för att komma igång:

* [ASP.NET-webbappar](./asp-net.md)
  * [Lägg till undantags övervakning](./asp-net-exceptions.md)
  * [Lägg till beroende övervakning](./monitor-performance-live-website-now.md)
* [Java EE-webbappar](./java-in-process-agent.md)

## <a name="exploring-performance-metrics"></a><a name="view"></a>Utforska prestanda mått
I [Azure Portal](https://portal.azure.com)bläddrar du till den Application Insights resurs som du har konfigurerat för ditt program. Bladet översikt visar grundläggande prestanda information:

Klicka på ett diagram om du vill se mer information och visa resultaten under en längre period. Klicka till exempel på panelen begär Anden och välj sedan ett tidsintervall:

![Klicka dig till mer data och välj ett tidsintervall](./media/web-monitor-performance/appinsights-48metrics.png)

Klicka på ett diagram om du vill välja vilka mått som visas, eller Lägg till ett nytt diagram och markera dess mått:

![Klicka på ett diagram om du vill välja mått](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Avmarkera alla mått** om du vill se hela det tillgängliga valet. Måtten hamnar i grupper; När någon medlem i en grupp har valts visas endast de andra medlemmarna i gruppen.

## <a name="what-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>Vad betyder det? Prestanda paneler och rapporter
Det finns olika prestanda mått som du kan hämta. Vi börjar med de som visas som standard på bladet program.

### <a name="requests"></a>Begäranden
Antalet mottagna HTTP-begäranden under en angiven period. Jämför detta med resultaten i andra rapporter för att se hur appen beter sig när belastningen varierar.

HTTP-begäranden innehåller alla GET-eller POST-förfrågningar för sidor, data och bilder.

Klicka på panelen för att hämta antalet för vissa URL: er.

### <a name="average-response-time"></a>Genomsnittlig svars tid
Mäter tiden mellan en webbdelsbegäran som anger ditt program och svaret som returneras.

Poängen visar ett glidande medelvärde. Om det finns många begär Anden kan det vara något som avviker från genomsnittet utan en uppenbar topp eller dip i grafen.

Sök efter ovanliga toppar. I allmänhet förväntar du svars tiden på att öka med en ökning av begär Anden. Om riset är oproportionerligt kan din app träffa en resurs gräns, till exempel CPU eller en tjänsts kapacitet som används.

Klicka på panelen för att hämta tider för vissa URL: er.

![Skärm bild av fönstret Application Health som visar linje diagram över tid för att flytta medelvärden för förfrågningar och svars tid.](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Långsammaste begär Anden
![Skärm bild av en lista med långsammaste förfrågningar och deras svars tider.](./media/web-monitor-performance/appinsights-44slowest.png)

Visar vilka begär Anden som kan kräva prestanda justering.

### <a name="failed-requests"></a>Misslyckade förfrågningar
![Skärm bild av ett linje diagram över tid för antalet misslyckade förfrågningar. det vill säga begär Anden som utlöste undantag som inte har fångats.](./media/web-monitor-performance/appinsights-46failed.png)

Antal begär Anden som utlöste undantag som inte har fångats.

Klicka på panelen om du vill se information om specifika problem och välj en enskild begäran om du vill visa information om den. 

Endast ett representativt urval av haverier bevaras för individuell granskning.

### <a name="other-metrics"></a>Andra mått
Om du vill se vilka andra mått du kan visa klickar du på en graf och avmarkerar sedan alla mått för att se den fullständiga tillgängliga uppsättningen. Klicka (i) om du vill se varje mått definition.

![Avmarkera alla mått om du vill se hela uppsättningen](./media/web-monitor-performance/appinsights-62allchoices.png)

Om du väljer något mått inaktive ras de andra som inte kan visas i samma diagram.

## <a name="set-alerts"></a>Ställa in avisering
För att få ett meddelande via e-post om ovanliga värden för alla mått, Lägg till en avisering. Du kan antingen välja att skicka e-postmeddelandet till konto administratörer eller till vissa e-postadresser.

![Skärm bild av dialog rutan Lägg till en varnings regel, tillsammans med skärm bilder som är anslutna via pilar som visar hur du når den dialog rutan från Metrics Explorer.](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Ange resursen före de andra egenskaperna. Välj inte webbtest-resurserna om du vill ange aviseringar för prestanda-eller användnings statistik.

Var noga med att anteckna vilka enheter som du uppmanas att ange tröskelvärdet för.

*Jag ser inte knappen Lägg till avisering.* – Är detta ett grupp konto som du har skrivskyddad åtkomst till? Kontakta konto administratören.

## <a name="diagnosing-issues"></a><a name="diagnosis"></a>Diagnostisera problem
Här följer några tips för att hitta och diagnostisera prestanda problem:

* Konfigurera [webbtester][availability] för att bli aviserad om din webbplats slutar fungera eller om den svarar felaktigt eller långsamt. 
* Jämför antalet begär Anden med andra mått för att se om felen eller långsamma svar är relaterade till belastningen.
* [Infoga och Sök efter spårnings uttryck][diagnostic] i koden för att hitta problem.
* Övervaka din webbapp i drift med [Live Metrics Stream][livestream].
* Avbilda statusen för ditt .NET-program med [Snapshot debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Hitta och åtgärda prestanda Flask halsar med prestanda undersökning

Du kan använda prestanda gransknings upplevelsen för att granska långsamma drifts åtgärder i din webbapp. Du kan snabbt välja en enskild långsam åtgärd och använda [profiler](./profiler.md) för att leda till att de långsamma åtgärderna går ned till kod. Med den nya varaktighets fördelningen som visas för den valda åtgärden kan du snabbt och enkelt utvärdera hur dåligt upplevelsen är för dina kunder. Du kan se hur många av dina användar interaktioner som har påverkats för varje långsam åtgärd. I följande exempel har vi beslutat att ta en närmare titt på hur du kan hämta kunder/information. I varaktighets fördelningen ser vi att det finns tre toppar. Insamlad vänster-insamling är cirka 400 MS och representerar bra svars upplevelser. Mellan insamling är cirka 1,2 s och representerar en mediocre upplevelse. 3,6 slutligen har vi en annan liten insamling som representerar 99 percentilen, vilket sannolikt skulle göra att våra kunder lämnar missnöjda. Den här upplevelsen är tio gånger långsammare än den fantastiska upplevelsen av samma åtgärd. 

![Hämta kunder/information tre varaktighets toppar](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Vi kan välja ett större tidsintervall för att få en bättre uppfattning om användar upplevelsen för den här åtgärden. Vi kan sedan också begränsa tids perioden för ett särskilt tidsintervall där åtgärden var långsam. I följande exempel har vi växlat från standardvärdet för 24 timmar till tidsintervallet sju dagar och zoomat in i fönstret 9:47 till 12:47 tids period mellan tis 12 och ons 13. Både varaktighets fördelningen och antalet prov-och profiler spår har uppdaterats till höger.

![Skärm bild som visar prestanda (för hands version) med tre varaktigheter i intervallet 7 dagar med ett tidsfönster](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

För att begränsa den långsamma upplevelsen kan vi härnäst zooma in de varaktigheter som ligger mellan 95 och 99 percentilen. Dessa representerar 4% av användar interaktioner som var långsamma.

![Skärm bild som visar ERHÅLLna kunder/information tre varaktigheter i intervallet 7 dagar med en tidsperiod.](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Vi kan nu antingen titta på de representativa exemplen genom att klicka på knappen exempel eller vid spårning av representativa profiler, genom att klicka på spårnings knappen för profiler. I det här exemplet finns fyra spår som har samlats in för Hämta kunder/information i tids perioden och intervallets varaktighet.

Ibland kommer problemet inte att finnas i din kod, utan i stället ett beroende av kod anrop. Du kan växla till fliken beroenden i vyn prestanda prioritering för att undersöka sådana långsamma beroenden. Som standard är vyn prestanda trenden i genomsnitt, men det du verkligen vill titta på är den 95 percentilen (eller 99, om du övervakar en vuxen tjänst). I följande exempel har vi fokuserat på det långsamma Azure BLOB-beroendet, där vi anropar skicka fabrikamaccount. Kluster med hög upplevelse runt 40 MS, medan långsamma anrop till samma beroende är tre gånger långsammare, klustring runt 120 MS. Det tar inte flera av dessa anrop att lägga till upp för att göra så att respektive åtgärd märkbart saktar ned. Du kan öka detalj nivån i representativa exempel och profiler spår, precis som du kan med fliken åtgärder.

![Hämta kunder/information tre varaktigheter i intervallet för 7 dagar med ett tidsfönster](./media/web-monitor-performance/SlowDependencies95thTrend.png)

Prestanda undersöknings upplevelsen visar relevanta insikter längs den exempel uppsättning som du beslutade att fokusera på. Det bästa sättet att titta på alla tillgängliga insikter är att växla till ett tidsintervall i 30 dagar och sedan välja övergripande för att se insikter över alla åtgärder under den senaste månaden.

![Skärm bild som visar prestanda (för hands version) med det övergripande fönstret öppet och insikter valt.](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next-steps"></a><a name="next"></a>Nästa steg
[Webbtester][availability] – låt webb förfrågningar skickas till ditt program med jämna mellanrum från hela världen.

[Avbilda och Sök diagnostiska spårningar][diagnostic] – infoga spårnings anrop och gå igenom resultaten för att hitta problem.

[Användnings spårning][usage] – ta reda på hur användare använder ditt program.

[Fel sökning][qna] och frågor och svar & A



<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ./live-stream.md
[snapshot]: ./snapshot-debugger.md

