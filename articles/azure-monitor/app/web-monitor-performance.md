---
title: Övervaka appens hälsa och användning med Application Insights
description: Kom igång med Application Insights. Analysera användningen, tillgängligheten och prestanda i din lokala eller Microsoft Azure-program.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 8f5a0a22ecea0b5df0f4108984b2a65d4add2bf8
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301782"
---
# <a name="monitor-performance-in-web-applications"></a>Övervaka prestanda i webbprogram


Kontrollera att ditt program fungerar och lär dig snabbt eventuella fel. [Application Insights] [ start] berättar om några prestandaproblem och undantag, och hjälper dig att hitta och diagnostisera den bakomliggande orsaken.

Application Insights kan övervaka Java- och ASP.NET-webbprogram och tjänster, WCF-tjänster. De kan vara hanteras lokalt, på virtuella datorer eller som Microsoft Azure-webbplatser. 

På klientsidan, kan Application Insights ta telemetri från webbsidor och en mängd olika enheter, inklusive iOS, Android och Windows Store-appar.

## <a name="setup"></a>Konfigurera övervakning av programprestanda
Om du inte har lagt till Application Insights i projektet (det vill säga om den inte redan har ApplicationInsights.config), väljer du något av dessa sätt att komma igång:

* [ASP.NET-webbappar](../../azure-monitor/app/asp-net.md)
  * [Lägg till undantagsövervakning](../../azure-monitor/app/asp-net-exceptions.md)
  * [Lägg till beroendeövervakning](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Java EE-webbappar](../../azure-monitor/app/java-get-started.md)
  * [Lägg till beroendeövervakning](../../azure-monitor/app/java-agent.md)

## <a name="view"></a>Utforska prestandamått
I [Azure-portalen](https://portal.azure.com), bläddra till Application Insights-resursen som du har konfigurerat för ditt program. Översiktsbladet visas grundläggande prestandadata:

Klicka på ett diagram för att se mer information och för att se resultat för en längre period. Klicka till exempel begäranden panelen och välj sedan ett tidsintervall:

![Klicka vidare till mer data och välj ett tidsintervall](./media/web-monitor-performance/appinsights-48metrics.png)

Klicka på ett diagram om du vill välja vilka mått som det visas, eller Lägg till ett nytt diagram och välja dess mått:

![Klicka på ett diagram om du vill välja mått](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Avmarkera alla mått** Visa fullständig urval som är tillgänglig. Mått faller inom grupper. När alla medlemmar i en grupp väljs, visas endast de andra medlemmarna i gruppen.

## <a name="metrics"></a>Vad betyder det alla? Prestanda-paneler och rapporter
Det finns olika prestandamått som du kan hämta. Låt oss börja med de som visas som standard på programbladet.

### <a name="requests"></a>Begäranden
Antal HTTP-begäranden som tas emot i en angiven period. Jämför detta med resultatet för andra rapporter att se hur din app fungerar när belastningen varierar.

HTTP-begäranden inkluderar alla GET eller POST-begäranden för sidor, data och avbildningar.

Klicka på ikonen för att hämta antalet för specifika URL: er.

### <a name="average-response-time"></a>Genomsnittlig svarstid
Mäter tiden mellan en webbegäran som att ange ditt program och svaret returneras.

Poäng visar det genomsnittliga en flytta dem. Om det finns många begäranden kan finnas det något som avviker från genomsnittliga utan en uppenbar högsta eller sjunker i diagrammet.

Leta efter ovanliga toppar. I allmänhet förväntar sig svarstid för att öka med en ökning av begäranden. Om den är oproportionerligt, kan din app ha drabbats av en resursgräns t.ex CPU eller kapaciteten för en tjänst som används.

Klicka på panelen för att få gånger för specifika URL: er.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>De långsammaste förfrågningarna
![](./media/web-monitor-performance/appinsights-44slowest.png)

Visar vilka förfrågningar kan behöva prestandajustering.

### <a name="failed-requests"></a>Misslyckade förfrågningar
![](./media/web-monitor-performance/appinsights-46failed.png)

Antalet begäranden som utlöste undantag utan felhantering.

Klicka på panelen för att se information om specifika problem och välj en enskild begäran att se dess information. 

Ett representativt urval fel sparas för enskilda granskning.

### <a name="other-metrics"></a>Andra mått
Om du vill se vad ange andra mått som du kan visa, klickar du på ett diagram och sedan avmarkera alla mått för att se fullständiga som är tillgängliga. Klicka på (i) om du vill se varje mått definition.

![Avmarkera alla mått för att se hela uppsättningen](./media/web-monitor-performance/appinsights-62allchoices.png)

Om du väljer vilka mått som helst inaktiveras andra som inte får förekomma i samma schema.

## <a name="set-alerts"></a>Ange aviseringar
Lägg till en avisering om du vill meddelas via e-post med ovanliga värden för alla mått. Du kan välja att skicka e-postmeddelandet till kontoadministratörer eller till specifika e-postadresser.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Ställ in resursen innan de andra egenskaperna. Välj inte webbtest resurser om du vill ställa in varningar för mått för prestanda och användning.

Var noga med att tänka på de enheter där du uppmanas att ange ett tröskelvärde.

*Knappen Lägg till avisering visas inte.* -Är detta en grupp konto som du har skrivskyddad åtkomst? Kontakta kontoadministratör.

## <a name="diagnosis"></a>Diagnostisera problem
Här följer några tips för att hitta och diagnostisera prestandaproblem:

* Konfigurera [webbtester] [ availability] att aviseras om webbplatsen stängs av eller svarar långsamt eller felaktigt. 
* Jämför antalet begäranden med andra mått för att se om misslyckade eller långsamma svar är relaterade till att läsa in.
* [Infoga och Sök spårningsinstruktioner] [ diagnostic] i din kod för att identifiera problem.
* Övervaka din webbapp på åtgärden med [Live Metrics Stream][livestream].
* Spara tillståndet för ditt .net-program med [Snapshot Debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Hitta och åtgärda flaskhalsar i prestanda med upplevelse med prestandaundersökning

Du kan använda upplevelsen med prestandaundersökning för att granska långsamma utför åtgärder i din webbapp. Du kan snabbt välja en viss långsam åtgärd och använda [Profiler](../../azure-monitor/app/profiler.md) till rot orsaka långsam åtgärderna ned kod. Med den nya varaktighetsfördelning som visas för den valda åtgärden kan du snabbt i korthet utvärdera hur allvarligt upplevelsen är för dina kunder. Du kan se hur många användarinteraktioner som har påverkats för varje långsam åtgärd. I exemplet nedan har vi valt att ta en närmare titt på upplevelsen för GET Customers/Details-åtgärden. Vi kan se att det finns tre toppar i varaktighet-distributionen. Längst till vänster topp är cirka 400 ms och representerar bra dynamiska upplevelse. Mitten topp är runt 1,2 s och representerar en medelmåttiga upplevelse. Slutligen på 3.6 s vi har en annan små topp som representerar den 99: e percentil-upplevelsen, som kan leda till våra kunder att lämna missnöjd. Den upplevelsen är tio gånger långsammare än den bästa upplevelsen för samma åtgärd. 

![GET Customers/Details tre varaktighet toppar](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Om du vill få en bättre uppfattning om användarupplevelser för den här åtgärden kan välja vi ett större tidsintervall. Vi kan sedan också upptäcka tidpunkt på ett specifikt tidsfönster där åtgärden gick långsamt. I följande exempel har vi växlat från standardvärdet 24 timmar tidsintervallet 7 dagar tidsintervall och sedan zoomar in 9:47 till 12:47 tidsfönstret mellan TIS 12 och ons 13. Både varaktighetsfördelning och antalet exemplet och profiler-spårningar har uppdaterats till höger.

![GET Customers/Details tre varaktighet toppar i 7 dagar intervall med en tidsperiod](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Om du vill begränsa i på långsamma upplevelser kan vi sedan zooma in i varaktigheter som faller mellan 95: e och 99: e percentilen. Dessa representerar 4-% av användarinteraktioner som var långsamt.

![GET Customers/Details tre varaktighet toppar i 7 dagar intervall med en tidsperiod](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Vi kan nu antingen titt på representativt exempel genom att klicka på knappen exempel eller på representativa profiler-spårningar genom att klicka på knappen Profiler-spårningar. I det här exemplet finns det fyra spårningar som har samlats in för GET Customers/Details i varaktighet för fönstret och intervallet av intresse.

Kan ibland inte problemet i koden, men i stället i ett beroende koden anropar. Du kan växla till fliken beroenden i prestandasorteringsvyn att undersöka sådana långsamma beroenden. Som standard är prestandavyn populära medelvärden, men vad du vill att titta på är den 95: e percentilen (eller 99th, om du övervakar en mogen tjänst). I följande exempel har vi fokuserat på att den långsamma Azure BLOB-beroenden där vi anropar PUT fabrikamaccount. Bästa upplevelser kluster cirka 40 ms, medan de långsamma anrop till samma beroendet är tre gånger långsammare, klustring cirka 120 ms. Du inte göra många av dessa anrop att lägga till till respektive åtgärden att märkbart långsammare. Du kan se representativt exempel och profiler-spårningar, precis som med fliken åtgärder.

![GET Customers/Details tre varaktighet toppar i 7 dagar intervall med en tidsperiod](./media/web-monitor-performance/SlowDependencies95thTrend.png)

Upplevelsen med prestandaundersökning visar relevant information längs sida exempeldata som du valt att fokusera på. Det bästa sättet att se alla tillgängliga insikter är att växla till ett tidsintervall för 30 dagar och välj sedan övergripande att se insikter över alla åtgärder för den senaste månaden.

![GET Customers/Details tre varaktighet toppar i 7 dagar intervall med en tidsperiod](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Nästa steg
[Webbtester] [ availability] -har begäranden skickas till programmet med jämna mellanrum från hela världen.

[Samla och söka diagnostikspårningar] [ diagnostic] – Spårningsanrop och gå igenom resultaten till att identifiera problem.

[Användningsspårning] [ usage] – ta reda på hur människor använder programmet.

[Felsöka] [ qna] - och frågor och svar



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



