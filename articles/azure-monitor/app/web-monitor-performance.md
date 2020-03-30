---
title: Övervaka appens hälsa och användning med Application Insights
description: Kom igång med Application Insights. Analysera användning, tillgänglighet och prestanda för dina lokala eller Microsoft Azure-program.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: e398f23559729580ae4ad8b6507c2f09328052b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275625"
---
# <a name="monitor-performance-in-web-applications"></a>Övervaka prestanda i webbprogram


Se till att ditt program fungerar bra och ta reda snabbt på eventuella fel. [Application Insights][start] berättar om eventuella prestandaproblem och undantag och hjälper dig att hitta och diagnostisera grundorsakerna.

Application Insights kan övervaka både Java och ASP.NET webbapplikationer och-tjänster, WCF-tjänster. De kan vara lokala, lokala datorer eller som Microsoft Azure-webbplatser. 

På klientsidan kan Application Insights ta telemetri från webbsidor och en mängd olika enheter, inklusive iOS-, Android- och Windows Store-appar.

## <a name="set-up-performance-monitoring"></a><a name="setup"></a>Ställ in prestandaövervakning
Om du ännu inte har lagt till Application Insights i projektet (det vill säga om det inte har ApplicationInsights.config) väljer du ett av följande sätt att komma igång:

* [ASP.NET-webbappar](../../azure-monitor/app/asp-net.md)
  * [Lägg till undantagsövervakning](../../azure-monitor/app/asp-net-exceptions.md)
  * [Lägg till beroendeövervakning](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Java EE-webbappar](../../azure-monitor/app/java-get-started.md)
  * [Lägg till beroendeövervakning](../../azure-monitor/app/java-agent.md)

## <a name="exploring-performance-metrics"></a><a name="view"></a>Utforska prestandamått
I [Azure-portalen](https://portal.azure.com)bläddrar du till den Application Insights-resurs som du konfigurerar för ditt program. Översiktsbladet visar grundläggande prestandadata:

Klicka på ett diagram om du vill se mer detaljer och se resultat under en längre period. Klicka till exempel på panelen Begäranden och sedan välja ett tidsintervall:

![Klicka dig vidare till fler data och välj ett tidsintervall](./media/web-monitor-performance/appinsights-48metrics.png)

Klicka på ett diagram för att välja vilka mått det ska visa, eller lägg till ett nytt diagram och välj dess mått:

![Klicka på ett diagram för att välja mått](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Avmarkera alla mått** för att se hela markeringen som är tillgänglig. Måtten delas in i grupper. När någon medlem i en grupp väljs visas endast de andra medlemmarna i den gruppen.

## <a name="what-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>Vad betyder allt? Prestandapaneler och rapporter
Det finns olika prestandamått som du kan få. Låt oss börja med dem som visas som standard på programbladet.

### <a name="requests"></a>Begäranden
Antalet HTTP-begäranden som tagits emot under en angiven period. Jämför detta med resultaten i andra rapporter för att se hur appen fungerar när belastningen varierar.

HTTP-begäranden omfattar alla GET- eller POST-begäranden för sidor, data och bilder.

Klicka på panelen för att få antal för specifika webbadresser.

### <a name="average-response-time"></a>Genomsnittlig svarstid
Mäter tiden mellan en webbbegäran som anger ditt program och svaret som returneras.

Poängen visar ett glidande medelvärde. Om det finns många förfrågningar kan det finnas några som avviker från genomsnittet utan en uppenbar topp eller dopp i diagrammet.

Leta efter ovanliga toppar. I allmänhet förväntar svarstiden att stiga med en ökning av förfrågningar. Om ökningen är oproportionerlig kan appen träffa en resursgräns som cpu eller kapaciteten för en tjänst som används.

Klicka på panelen för att få tider för specifika webbadresser.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Långsammaste begäranden
![](./media/web-monitor-performance/appinsights-44slowest.png)

Visar vilka begäranden som kan behövas prestandajustering.

### <a name="failed-requests"></a>Misslyckade förfrågningar
![](./media/web-monitor-performance/appinsights-46failed.png)

En räkning av begäranden som kastade uncaught undantag.

Klicka på panelen om du vill se information om specifika fel och välj en enskild begäran för att se dess detaljer. 

Endast ett representativt urval av fel behålls för individuell inspektion.

### <a name="other-metrics"></a>Andra mått
Om du vill se vilka andra mått du kan visa klickar du på ett diagram och avmarkerar sedan alla mått för att se den fullständiga tillgängliga uppsättningen. Klicka på (i) om du vill se varje måttdefinition.

![Avmarkera alla mått för att se hela uppsättningen](./media/web-monitor-performance/appinsights-62allchoices.png)

Om du väljer ett mått inaktiveras de andra som inte kan visas i samma diagram.

## <a name="set-alerts"></a>Ange aviseringar
Om du vill meddelas via e-post om ovanliga värden för ett mått lägger du till en avisering. Du kan välja att antingen skicka e-postmeddelandet till kontoadministratörerna eller till specifika e-postadresser.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Ange resursen före de andra egenskaperna. Välj inte de webbtestresurser om du vill ställa in aviseringar om prestanda- eller användningsmått.

Var noga med att notera de enheter där du uppmanas att ange tröskelvärdet.

*Knappen Lägg till avisering visas inte.* - Är det här ett gruppkonto som du har skrivskyddad åtkomst till? Kontakta kontoadministratören.

## <a name="diagnosing-issues"></a><a name="diagnosis"></a>Diagnostisera problem
Här är några tips för att hitta och diagnostisera prestandaproblem:

* Konfigurera [webbtester][availability] så att den får aviseringar om webbplatsen går ner eller svarar felaktigt eller långsamt. 
* Jämför antalet begäranden med andra mått för att se om fel eller långsam respons är relaterade till inläsning.
* [Infoga och sök spårningssatser][diagnostic] i koden för att identifiera problem.
* Övervaka din webbapp i drift med [Live Metrics Stream][livestream].
* Samla in tillståndet för DITT .NET-program med [Snapshot Debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Hitta och åtgärda flaskhalsar med prestandaundersökning

Du kan använda prestandaundersökningsupplevelsen för att granska långsamma åtgärder i webbappen. Du kan snabbt välja en viss långsam åtgärd och använda [Profiler](../../azure-monitor/app/profiler.md) för att grunda orsaka långsamma åtgärder ner till kod. Med hjälp av den nya varaktighetsfördelningen som visas för den valda åtgärden kan du snabbt bedöma hur dålig upplevelsen är för dina kunder. Du kan se hur många av dina användarinteraktioner som påverkades för varje långsam åtgärd. I följande exempel har vi beslutat att ta en närmare titt på upplevelsen för GET Customers/Details-åtgärden. I varaktighetsfördelningen kan vi se att det finns tre spikar. Leftmost spike är runt 400 ms och representerar stor lyhörd upplevelse. Middle spike är runt 1,2 s och representerar en medioker upplevelse. Slutligen på 3,6 s har vi en annan liten spik som representerar den 99: e percentilen erfarenhet, vilket sannolikt kommer att orsaka våra kunder att lämna missnöjda. Den erfarenheten är tio gånger långsammare än den stora upplevelsen för samma operation. 

![FÅ Kunder/Detaljer tre varaktighetsspikar](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

För att få en bättre känsla för användarupplevelser för den här åtgärden kan vi välja ett större tidsintervall. Vi kan då också begränsa i tid på ett visst tidsfönster där operationen var långsam. I följande exempel har vi växlat från standardtidsintervallet på 24 timmar till tidsintervallet på 7 dagar och sedan zoomat in i tidsfönstret 9:47 till 12:47 mellan tis den 12:e och ons den 13:e. Både varaktighetsfördelningen och antalet prov- och profilerspårningar har uppdaterats till höger.

![FÅ Kunder/Detaljer tre varaktighetsspikar i 7 dagars intervall med ett tidsfönster](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

För att begränsa in på långsamma upplevelser, vi nästa zooma in i de löptider som faller mellan 95: e och 99: e percentilen. Dessa representerar 4% av användarinteraktioner som var långsamma.

![FÅ Kunder/Detaljer tre varaktighetsspikar i 7 dagars intervall med ett tidsfönster](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Vi kan nu antingen titta på de representativa exemplen, genom att klicka på knappen Exempel, eller på den representativa profiler spår, genom att klicka på Profiler spår knappen. I det här exemplet finns det fyra spårningar som har samlats in för GET-kunder/information i tidsfönstret och intervallets varaktighet.

Ibland kommer problemet inte att finnas i koden, utan i ett beroende av dina kodanrop. Du kan växla till fliken Beroenden i prestandatriage-vyn för att undersöka sådana långsamma beroenden. Som standard är prestandavyn trendande medelvärden, men vad du verkligen vill titta på är den 95:e percentilen (eller den 99:e, om du övervakar en mogen tjänst). I följande exempel har vi fokuserat på det långsamma Azure BLOB-beroendet, där vi anropar PUT fabrikamaccount. De goda upplevelserna kluster runt 40 ms, medan långsamma anrop till samma beroende är tre gånger långsammare, klustring runt 120 ms. Det tar inte många av dessa samtal för att lägga upp för att orsaka respektive operation att märkbart sakta ner. Du kan öka detaljnivån i representativa exempel och profilerspårningar, precis som med fliken Operationer.

![FÅ Kunder/Detaljer tre varaktighetsspikar i 7 dagars intervall med ett tidsfönster](./media/web-monitor-performance/SlowDependencies95thTrend.png)

Prestandaundersökningsupplevelsen visar relevanta insikter längs sidan av provuppsättningen som du bestämde dig för att fokusera på. Det bästa sättet att titta på alla tillgängliga insikter är att växla till en 30 dagars tidsintervall och sedan välja Övergripande för att se insikter över alla operationer för den senaste månaden.

![FÅ Kunder/Detaljer tre varaktighetsspikar i 7 dagars intervall med ett tidsfönster](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next-steps"></a><a name="next"></a>Nästa steg
[Webbtester][availability] - Har webbförfrågningar skickade till din ansökan med jämna mellanrum från hela världen.

[Fånga och sök diagnostiska spår][diagnostic] - Infoga spårningsanrop och sålla igenom resultaten för att identifiera problem.

[Användningsspårning][usage] - Ta reda på hur personer använder ditt program.

[Felsökning][qna] - och Q & A



<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ../../azure-monitor/app/live-stream.md
[snapshot]: ../../azure-monitor/app/snapshot-debugger.md



