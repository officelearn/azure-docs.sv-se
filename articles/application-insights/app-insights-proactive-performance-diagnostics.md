---
title: Smartkort identifiering - prestandaavvikelser | Microsoft Docs
description: Application Insights utför smart analys av din app telemetri och varnar dig om potentiella problem. Den här funktionen behöver ingen installation.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin; antonfr
ms.openlocfilehash: 59b88a940e83ed89e8638b62680a57ca1514f8b0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="smart-detection---performance-anomalies"></a>Identifiering - Prestandaavvikelser för smartkort

[Application Insights](app-insights-overview.md) automatiskt analyserar prestanda för ditt webbprogram och varna dig om potentiella problem. Du kanske att läsa det eftersom du har fått en av våra meddelanden för smart identifiering.

Den här funktionen kräver några särskilda inställningar, förutom att konfigurera din app för Application Insights (på [ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), eller [Node.js](app-insights-nodejs.md), och i [webbsida kod](app-insights-javascript.md)). Det är aktivt när genererar tillräckligt med telemetri för din app.

## <a name="when-would-i-get-a-smart-detection-notification"></a>När kan jag får ett meddelande om smarta identifiering?

Application Insights har upptäckt att prestanda för ditt program har degraderats i något av följande sätt:

* **Svaret tid försämring** -appen har startats långsammare än den som används för att svara på begäranden. Ändringen kan ha varit snabb, till exempel eftersom det var en regressionsmodell i senaste distributionen. Eller så kanske har blivit gradvis, kanske på grund av en minnesläcka. 
* **Beroende varaktighet försämring** -appen gör anrop till en REST-API, databaser eller andra beroende. Beroendet är långsammare än den som används för att svara.
* **Långsam mönster** -appen verkar ha ett prestandaproblem som påverkar endast vissa begäranden. Till exempel inläsning sidor långsammare på en typ av webbläsare än andra. eller begäranden behandlas långsammare från en viss server. För närvarande titta våra algoritmer på sidinläsningstider, begäran svarstider och beroende svarstider.  

Smart identifiering kräver minst 8 dagar telemetri vid en fungerande volym för att upprätta en baslinje för normal prestanda. När programmet har körts under den tid, så resulterar eventuella viktiga problem i ett meddelande.


## <a name="does-my-app-definitely-have-a-problem"></a>Har min app definitivt problem?

Nej, ett meddelande innebär inte att appen verkligen har problem. Det är bara ett förslag om något du kanske vill titta på flera.

## <a name="how-do-i-fix-it"></a>Hur kan jag göra?

Aviseringarna inkluderar diagnostisk information. Här är ett exempel:


![Här är ett exempel på servern svar tid försämring identifiering](./media/app-insights-proactive-diagnostics/server_response_time_degradation.png)

1. **Prioritering**. Meddelandet visas hur många användare eller hur många åtgärder som påverkas. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Scope**. Problemet som påverkar all trafik, eller bara vissa sidor Begränsas den viss webbläsare eller platser? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera**. Ofta föreslås diagnostisk information i meddelandet problemets art. Om svarstiden saktar ned när frågehastigheten är för hög, föreslår som till exempel din server eller beroenden är överbelastad. 

    I annat fall öppna bladet prestanda i Application Insights. Där hittar du [Profiler](app-insights-profiler.md) data. Om undantag utlöses, du kan också prova den [ögonblicksbild felsökare](app-insights-snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Meddelanden för smart identifiering är aktiverad som standard och skickas till de som har [ägare, deltagare och läsare som har åtkomst till Application Insights-resursen](app-insights-resources-roles-access-control.md). Om du vill ändra den här, antingen på **konfigurera** i e-postmeddelande eller öppna smarta identifieringsinställningar i Application Insights. 
  
  ![Identifieringsinställningar för smartkort](./media/app-insights-proactive-diagnostics/smart_detection_configuration.png)
  
  * Du kan använda den **avbryta prenumerationen** länk i e-Smart identifiering att inte ta emot e-postmeddelanden.

E-post om identifieringar som Smart prestandaavvikelser är begränsade till en e-post per dag per Application Insights-resurs. E-postmeddelandet skickas endast om det finns minst en ny problem som har identifierats på den dagen. Du kommer inte upprepas för varje meddelande. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

* *Alltså Se Microsoft-Personal på Mina data?*
  * Nej. Tjänsten är helt automatisk. Bara hämta meddelanden. Dina data är [privata](app-insights-data-retention-privacy.md).
* *Du analysera alla data som samlas in av Application Insights?*
  * Inte för närvarande. För närvarande kan analyserar vi begäran svarstid, beroende-svarstid och sidan hämtningstid. Analys av ytterligare mått finns på vår eftersläpning väntar.

* Vilka typer av program fungerar detta för?
  * Dessa degradations identifieras i alla program som genererar lämpliga telemetri. Om du har installerat Application Insights i ditt webbprogram sedan spåras begäranden och beroenden automatiskt. Men i backend-tjänster eller andra appar, om du har infogat anrop till [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) eller [TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency), och sedan Smart identifiering fungerar på samma sätt.

* *Kan jag skapa egna avvikelseidentifiering regler för identifiering eller anpassa befintliga regler?*

  * Inte ännu, men du kan:
    * [Ställa in aviseringar](app-insights-alerts.md) som anger om måttet överskrider ett tröskelvärde.
    * [Exportera telemetri](app-insights-export-telemetry.md) till en [databasen](app-insights-code-sample-export-sql-stream-analytics.md) eller [till PowerBI](app-insights-export-power-bi.md), där du kan analysera dem själv.
* *Hur ofta utförs analysen?*

  * Vi kör dagligen analys på telemetrin från föregående dag (fullständig dag i UTC-tidszonen).
* *Det gör även detta ersätta [mått aviseringar](app-insights-alerts.md)?*
  * Nej.  Vi att inte identifiera var problemet bör du överväga onormalt.


* *Om jag inte göra något svar på ett meddelande ska jag en påminnelse?*
  * Nej, du får ett meddelande om varje problemet bara en gång. Om problemet kvarstår kommer att uppdateras i Smart identifieringen feed bladet.
* *E-postmeddelandet gick förlorad. Var hittar jag meddelanden i portalen*
  * I Application Insights-översikten över appen klickar du på den **Smart identifiering** panelen. Det kommer du att kunna hitta alla meddelanden upp till 90 dagar bakåt.

## <a name="how-can-i-improve-performance"></a>Hur kan jag för att förbättra prestanda?
Långsamt och misslyckade svar är en av de största frustrationen för webbplatsanvändare som du känner till från din egen erfarenheter. Därför är det viktigt att åtgärda eventuella problem.

### <a name="triage"></a>Prioritering
Först spelar det roll? Om en sida är alltid tar lång tid att läsa in, men endast 1% av webbplatsens användare behöver titta på den, kanske har du flera viktiga saker att tänka på. Å andra sidan, om det bara är 1% av användare öppna den, men den utlöser undantag varje gång, kan det vara värt att undersöka.

Använda instruktionen påverkan (användare som påverkas eller % av trafik) som en allmän vägledning, men tänk på att den inte hela artikeln. Samla in andra bevis för att bekräfta.

Överväg att parametrarna för problemet. Om det är beroende geografi, ställa in [tillgänglighetstester](app-insights-monitor-web-app-availability.md) inklusive den regionen: det kanske bara nätverksproblem i detta område.

### <a name="diagnose-slow-page-loads"></a>Diagnostisera långsam sidan läses in
Var har problemet uppstått? Är servern svarar långsamt, är sidan lång eller webbläsaren behöver göra mycket arbete för att visa den?

Öppna bladet webbläsare mått. Segmenterade visningen av webbläsaren belastningen tid visas där tiden kommer. 

* Om **skicka tid för begäran** är hög, antingen servern svarar långsamt eller begäran är en post med stora mängder data. Titta på den [prestandamått](app-insights-web-monitor-performance.md#metrics) att undersöka svarstider.
* Ställ in [beroende spårning](app-insights-asp-net-dependencies.md) att se om den långsamt arbete är på grund av externa tjänster eller din databas.
* Om **ta emot svar** är övervägande, sidan och dess beroende delar - JavaScript, CSS, bilder och så vidare (men inte asynkront inlästa data) är för lång. Konfigurera en [tillgänglighetstest](app-insights-monitor-web-app-availability.md), och se till att ange alternativet för att läsa in beroende delar. När du får några resultat kan öppna informationen som ett resultat och expandera den för att se laddningstider olika filer.
* Hög **klienten bearbetningstid** föreslår skript körs långsamt. Om orsaken till inte visas, Överväg att lägga till kod tidsinställning och skicka tiderna i trackMetric-anrop.

### <a name="improve-slow-pages"></a>Förbättra långsamma sidor
Det finns en fullständig råd om att förbättra din serversvar och sidinläsningstider, så inte försöker vi Upprepa den här alla webbplats. Här följer några tips du förmodligen redan vet om, precis som du tänker:

* Långsam lästes in på grund av stora filer: läsa in skript och andra delar asynkront. Använd skriptet paketering. Bryt huvudsidan i widgetar som läser in sina data separat. Skicka inte vanlig gamla HTML för långa tabeller: använda ett skript för att begära data som JSON eller andra komprimerat format och sedan fylla tabellen på plats. Det är bra ramverk för att hjälpa med allt detta. (De även innebära ett stort skript naturligtvis.)
* Långsamma server beroenden: Överväg att geografiska platser av komponenter. Till exempel om du använder Azure Kontrollera webbservern och databasen är i samma region. Frågor ska hämta mer information än de behöver? Skulle cachelagrar eller batchbearbetning hjälp?
* Kapacitet problem: Leta serverstatistik av svarstider och begäran räknas. Om svarstider högsta oproportionerligt med toppar i begäran räknas, är det troligt att servrarna har sträckts ut.


## <a name="server-response-time-degradation"></a>Servern svar tid försämring

Svaret tid försämring meddelandet kan du se:

* Svarstiden jämfört med normala svarstiden för den här åtgärden.
* Hur många användare påverkas.
* Genomsnittlig svarstid och 90: e percentilen svarstiden för den här åtgärden på dagen för att upptäcka och 7 dagar innan. 
* Antalet för den här åtgärden begäranden på dagen för att upptäcka och 7 dagar innan.
* Korrelation mellan försämring i den här åtgärden och degradations i relaterade beroenden. 
* Länkar som hjälper dig att felsöka problemet.
  * Profiler-spårningar för att visa där åtgärden tid det tar (länken är tillgänglig om profileraren trace exempel samlades in för den här åtgärden under identifieringsperioden). 
  * Prestandarapporter i måttet Explorer, där du kan statistikforskning tid intervallfilter för den här åtgärden.
  * Sök efter det här anropet till specifika anropet egenskaper.
  * Fel rapporteras - om räkna > 1 detta innebära att det inträffade fel under den här åtgärden kan ha bidragit till försämrade prestanda.

## <a name="dependency-duration-degradation"></a>Beroende varaktighet försämring

Moderna program anta mer och mer micro services Designmetoden, vilket i många fall leder till hög tillförlitlighet för externa tjänster. Till exempel om ditt program som förlitar sig på vissa dataplattform eller även om skapar du egna bot på tjänsten som du förmodligen vidarebefordrar på vissa kognitiva tjänsteleverantör att aktivera din robotar kan interagera på flera mänsklig sätt och vissa data store-tjänsten för bot och hämtar svar från m.  

Exempel beroende omvandlings-meddelande:

![Här är ett exempel på beroende varaktighet försämring identifiering](./media/app-insights-proactive-diagnostics/dependency_duration_degradation.png)

Observera att du får meddelande:

* Varaktigheten jämfört med normala svarstiden för den här åtgärden
* Hur många användare som påverkas
* Genomsnittlig varaktighet och 90: e percentilen varaktigheten för detta beroende på dagen för att upptäcka och 7 dagar innan
* Antal beroende anrop på dagen för att upptäcka och 7 dagar innan
* Länkar som hjälper dig att diagnostisera problemet
  * Prestandarapporter i måttet Explorer för detta beroende
  * Sök efter beroendeanrop för att visa egenskaper för anrop
  * Rapporter om fel - om antalet > 1 Detta innebär att det fanns misslyckade beroende anrop under identifieringsperioden som kanske har bidragit till varaktighet försämras. 
  * Öppna Analytics med frågor som utför den här beroende varaktighet och antal  

## <a name="smart-detection-of-slow-performing-patterns"></a>Smart identifiering av långsam prestanda mönster 

Application Insights söker efter prestandaproblem som påverkar endast en del av användarna eller påverkar endast användare i vissa fall. Till exempel meddelanden om sidor belastningen är långsammare på en typ av webbläsare än på andra typer av webbläsare, eller om begäranden som betjänats långsammare från en viss server. Problem med kombinationer av egenskaper, kan också identifieras som långsam sidan läses in i ett geografiskt område för klienter som använder operativsystemet.  

Avvikelser som dessa är mycket svåra att upptäcka genom att kontrollera data, men är vanligare än du tror. De yta ofta endast när dina kunder klagar. Vid den tidpunkten är för sent: berörda användare redan växla till konkurrenter!

För närvarande titta våra algoritmer på sidinläsningstider, svarstider för begäran på servern och beroende svarstider.  

Du behöver inte ange några tröskelvärden eller konfigurera regler. Machine learning och datautvinningsalgoritmer används för att upptäcka onormala mönster.

![Klicka på länken om du vill öppna diagnostisk rapport i Azure e-postavisering](./media/app-insights-proactive-performance-diagnostics/03.png)

* **När** visas den tid som problemet upptäcktes.
* **Vad** beskrivs:

  * Problem som har identifierats;
  * Egenskaper för de händelser som påträffades visas problemet.
* I tabell jämförs ineffektivt fungerande uppsättningen med genomsnittlig beteendet för alla andra händelser.

Klicka på länkarna om du vill öppna mått Explorer och Sök på relevanta rapporter, filtreras i tid och egenskaperna för uppsättningen långsam prestanda.

Ändra tidsintervall och filter för att utforska telemetrin.

## <a name="next-steps"></a>Nästa steg
Dessa verktyg för Nätverksdiagnostik hjälpa dig att inspektera telemetri från din app:

* [Profiler](app-insights-profiler.md) 
* [Snapshot-felsökare](app-insights-snapshot-debugger.md)
* [Analys](app-insights-analytics-tour.md)
* [Diagnostik för Analytics-smartkort](app-insights-analytics-diagnostics.md)

Smart identifieringar är helt automatisk. Men kanske du vill ställa in några fler aviseringar?

* [Manuellt konfigurerade mått aviseringar](app-insights-alerts.md)
* [Tillgänglighetstester för webbprogram](app-insights-monitor-web-app-availability.md)
