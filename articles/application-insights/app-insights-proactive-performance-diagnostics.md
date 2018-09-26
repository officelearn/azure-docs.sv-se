---
title: Smart identifiering – prestandaavvikelser | Microsoft Docs
description: Application Insights gör smart analys av apptelemetrin och varnar dig om potentiella problem. Den här funktionen måste behöver konfigureras.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.author: mbullwin
ms.openlocfilehash: 8c34696f81e40b4db13e6868f6bdaa1b2a8e7a2a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095471"
---
# <a name="smart-detection---performance-anomalies"></a>Smart identifiering – Prestandaavvikelser

[Application Insights](app-insights-overview.md) analyseras prestanda för ditt webbprogram automatiskt och kan varna dig om potentiella problem. Du kan vara att läsa det eftersom du har en av våra smart identifiering meddelanden.

Den här funktionen kräver några särskilda inställningar, förutom att konfigurera din app för Application Insights (på [ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), eller [Node.js](app-insights-nodejs.md), och i [webbsida kod](app-insights-javascript.md)). Den är aktiv när din app genererar mycket telemetri.

## <a name="when-would-i-get-a-smart-detection-notification"></a>När ska jag får ett meddelande för smart identifiering?

Application Insights har identifierat att prestanda för programmet har degraderat i något av följande sätt:

* **Svaret tid försämring** -appen har börjat svara på begäranden långsammare än vad det brukade. Ändringen kanske har snabb, till exempel, eftersom det inte fanns en regression i din senaste distribution. Eller den kan ha gått gradvis, kanske på grund av en minnesläcka. 
* **Försämring av varaktigheten för beroendet** -appen anropar ett REST-API, databas eller andra beroenden. Beroendet svarar långsammare än vad det brukade.
* **Långsamma prestanda mönstret** -appen verkar ha prestandaproblem som påverkar endast vissa begäranden. Till exempel inläsning sidor långsammare på en typ av webbläsare än andra. eller begäranden behandlas långsammare från en viss server. För närvarande kan titta våra algoritmer på sidans belastning, svarstider för begäran och beroende svarstider.  

Smart identifiering kräver minst 8 dagarnas telemetri vid en fungerande volym för att upprätta en baslinje för normal prestanda. När ditt program har körts under den perioden, resulterar därför några betydande problem i ett meddelande.


## <a name="does-my-app-definitely-have-a-problem"></a>Min app definitivt finns det ett problem?

Nej, ett meddelande innebär inte att din app definitivt finns ett fel. Det är bara ett förslag om något kanske du vill titta på mer noggrant.

## <a name="how-do-i-fix-it"></a>Hur jag för att åtgärda det?

Aviseringarna inkluderar diagnostisk information. Här är ett exempel:


![Här är ett exempel på identifiering av Serversvarstider](./media/app-insights-proactive-diagnostics/server_response_time_degradation.png)

1. **Prioritering**. Aviseringen visar hur många användare eller hur många åtgärder som påverkas. Detta kan du tilldela en prioritet till problemet.
2. **Omfång**. Problemet som påverkar all trafik, eller enbart på vissa sidor Är den begränsad till viss webbläsare eller platser? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera**. Diagnostisk information i meddelandet föreslås ofta problemets art. Om svarstiden saktar ned när frågehastigheten är för hög, föreslår som till exempel din server eller beroenden är överbelastad. 

    I annat fall öppna bladet prestanda i Application Insights. Där hittar du [Profiler](app-insights-profiler.md) data. Om undantag utlöses, du kan också prova den [felsökning av ögonblicksbild](app-insights-snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Smart identifiering av meddelanden är aktiverade som standard och skickas till de som har [ägare, deltagare och läsare som har åtkomst till Application Insights-resursen](app-insights-resources-roles-access-control.md). Om du vill ändra detta, antingen på **konfigurera** i e-postmeddelande eller öppna inställningar för Smart identifiering i Application Insights. 
  
  ![Inställningar för smart identifiering](./media/app-insights-proactive-diagnostics/smart_detection_configuration.png)
  
  * Du kan använda den **avbryta prenumerationen** länken i e-postmeddelandet för Smart identifiering som sluta ta emot e-postmeddelanden.

E-post om identifieringar Smart prestandaavvikelser är begränsade till en e-post per dag per Application Insights-resurs. E-postmeddelandet skickas endast om det finns minst en nya ärenden som har identifierats på den dagen. Du kommer inte upprepningar av alla meddelanden. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

* *Det Microsoft-Personal titta på Mina data?*
  * Nej. Tjänsten är helt automatisk. Du får endast meddelanden. Dina data är [privata](app-insights-data-retention-privacy.md).
* *Du analysera alla data som samlas in av Application Insights?*
  * Inte för närvarande. För närvarande kan analysera vi begäran svarstid, svarstid för beroende och sidan kan du läsa in tid. Analys av ytterligare mått är på våra återstående uppgifter som vi ser fram emot.

* Vilka typer av programmet fungerar detta för?
  * Dessa försämringar identifieras i alla program som genererar den lämpliga telemetrin. Om du har installerat Application Insights i din webbapp, sedan spåras begäranden och beroenden automatiskt. Men i backend-tjänster eller andra appar, om du har infogat anrop till [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) eller [TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency), Smart identifiering fungerar på samma sätt.

* *Kan jag skapa min egen avvikelseidentifiering regler för identifiering eller anpassa befintliga regler?*

  * Inte ännu, men du kan:
    * [Ställa in aviseringar](app-insights-alerts.md) som talar om när ett mått överskrider ett tröskelvärde.
    * [Exportera telemetri](app-insights-export-telemetry.md) till en [databasen](app-insights-code-sample-export-sql-stream-analytics.md) eller [till PowerBI](app-insights-export-power-bi.md), där du kan analysera dem själv.
* *Hur ofta utförs analysen?*

  * Analys körs dagligen på telemetri från föregående dag (hel dag i UTC-tidszonen).
* *Det gör även detta ersätta [måttaviseringar](app-insights-alerts.md)?*
  * Nej.  Vi arbetar inte för att identifiera varje beteenden som du kan överväga att onormalt.


* *Får jag en påminnelse om jag inte gör något som svar på ett meddelande?*
  * Nej, du får ett meddelande om varje problemet bara en gång. Om problemet kvarstår kommer att uppdateras i Smart identifiering feed-bladet.
* *E-postmeddelandet gick förlorad. Var hittar jag aviseringar i portalen?*
  * I Application Insights-översikten över appen klickar du på den **Smart identifiering** panelen. Det kommer du att kunna hitta alla meddelanden upp till 90 dagar tillbaka.

## <a name="how-can-i-improve-performance"></a>Hur kan jag för att förbättra prestanda?
Långsam och misslyckade svar är en av största frustrationen för webbplatsanvändare som du vet från din egen erfarenhet. Det är därför viktigt att åtgärda eventuella problem.

### <a name="triage"></a>Prioritering
Först, spelar det någon roll? Om en sida är alltid långsamt att läsa, men endast 1% av webbplatsens användare behöver titta på den, har du kanske är viktigare saker att tänka på. Om det bara 1% av användarna öppna den, men den genererar undantag varje gång, kanske som å andra sidan värda att undersöka.

Använda instruktionen påverkan (berörda användare eller % av trafiken) som en allmän vägledning, men tänk på att det inte är hela artikeln. Samla in andra bevis på att bekräfta.

Överväg att parametrarna för problemet. Om det är beroende av geografiskt område, ställa in [tillgänglighetstester](app-insights-monitor-web-app-availability.md) inklusive regionen: Det kan bara finnas nätverksproblem i det området.

### <a name="diagnose-slow-page-loads"></a>Diagnostisera långsamma sidinläsningar
Var finns problemet? Långsamt servern svarar, är sidan som är mycket långa eller har webbläsaren att göra mycket arbete att visa den?

Öppna bladet webbläsare mått. Segmenterade visningen av webbläsare sidan belastningen tid visar vart tiden är på väg. 

* Om **skicka begärandetid** är hög, antingen servern svarar långsamt eller begäran finns ett inlägg med stora mängder data. Titta på den [prestandamått](app-insights-web-monitor-performance.md#metrics) att undersöka svarstider.
* Konfigurera [beroendespårning](app-insights-asp-net-dependencies.md) att se om långsamhet beror på att externa tjänster eller din databas.
* Om **ta emot svar** är dominerande, sidan och dess beroende delar – JavaScript, CSS, bilder och så vidare (men inte asynkront inlästa data) är långa. Konfigurera en [tillgänglighetstestet](app-insights-monitor-web-app-availability.md), och se till att ställa in alternativet för att läsa in beroende delar. När du får några resultat, öppna informationen om ett resultat och expandera den för att se inläsningstider olika filer.
* Hög **klienten bearbetningstid** föreslår skript körs långsamt. Om orsaken till inte visas, Överväg att lägga till kod tidsinställning och sändtider i trackMetric anrop.

### <a name="improve-slow-pages"></a>Förbättra långsamma sidor
Det finns en webbplats som är fulla av råd om att förbättra dina serversvar och sidinläsningstider, så att vi inte försöka Upprepa det allt här. Här följer några tips som du förmodligen redan vet om, bara för att få funderar du:

* Långsam inläsning av på grund av stora filer: läsa in skripten och andra delar asynkront. Använd skriptet paketering. Bryt huvudsidan i widgetar som läses in sina data separat. Skicka inte vanlig gamla HTML för långa tabeller: använda ett skript för att begära data som JSON eller andra kompakt format och sedan fylla i tabell på plats. Det är bra ramverk som behövs för att hjälpa dig med allt detta. (De också kan medföra big skript naturligtvis.)
* Långsam serverberoenden: Överväg att de geografiska platserna av dina komponenter. Till exempel om du använder Azure, se till webbservern och databasen är i samma region. Frågorna hämtar mer än de behöver? Skulle cachelagring eller batchbearbetning hjälp?
* Kapacitet problem: Titta på servermått för svarstider och antalet begäranden. Om svarstider högsta oproportionerligt toppar i antalet förfrågningar, är det troligt att dina servrar sträcks ut.


## <a name="server-response-time-degradation"></a>Serversvarstider

Svaret tid försämring meddelandet kan du se:

* Svarstiden jämfört med normala svarstiden för den här åtgärden.
* Hur många användare som påverkas.
* Genomsnittlig svarstid och 90: e percentilen svarstiden för den här åtgärden på dagen för att upptäcka och 7 dagar innan. 
* Antal den här åtgärden begäranden på dag av identifieringen och 7 dagar innan.
* Korrelation mellan försämring i den här åtgärden och försämringar i relaterade beroenden. 
* Länkar för att hjälpa dig att diagnostisera problemet.
  * Profiler-spårningar för att visa var åtgärden tid går åt (länken är tillgänglig om Profiler-spårning exempel samlades in för den här åtgärden under identifieringsperioden). 
  * Prestandarapporter i Metric Explorer, där du kan statistikforskning tid/intervallfiltren för den här åtgärden.
  * Sök efter det här anropet att visa specifika anrop egenskaper.
  * Rapporterar fel - om antal > 1 detta innebära att det fanns fel i den här åtgärden kan ha bidragit till försämrade prestanda.

## <a name="dependency-duration-degradation"></a>Försämring av varaktigheten för beroendet

Moderna program vidta fler och fler metod för mikrotjänster, vilket ofta leder till stor tillförlitlighet på externa tjänster. Till exempel om ditt program som förlitar sig på vissa dataplattform och även om skapar du egna bot på tjänsten som du förmodligen vidarebefordrar på vissa cognitive services-providern för att aktivera dina bots kan interagera på mänskligare sätt och vissa data store-tjänsten att hämta svar från-robot m.  

Exempel beroende försämring meddelande:

![Här är ett exempel på försämring av varaktigheten för beroendet identifiering](./media/app-insights-proactive-diagnostics/dependency_duration_degradation.png)

Observera att du:

* Varaktigheten jämfört med normala svarstiden för den här åtgärden
* Hur många användare som påverkas
* Genomsnittlig varaktighet och 90: e percentilen längden på det här beroendet på dag av identifieringen och 7 dagar innan
* Antalet beroende anrop på dag av identifieringen och 7 dagar innan
* Länkar som hjälper dig att diagnostisera problemet
  * Prestandarapporter i Metric Explorer för det här beroendet
  * Sök efter det här beroendet anropar för att visa egenskaperna för anrop
  * Rapporter om fel - om antal > 1 Det innebär att det fanns misslyckat beroende anrop under identifieringsperioden som kan ha bidragit till försämring av varaktigheten. 
  * Öppna Analytics med frågor som utför den här beroendevaraktighet och antal  

## <a name="smart-detection-of-slow-performing-patterns"></a>Smart identifiering av långsam prestanda mönster 

Application Insights söker efter prestandaproblem som kan påverka endast en del av användarna eller påverkar endast användare i vissa fall. Till exempel om sidor är långsammare för en typ av webbläsare än på andra typer av webbläsare, eller om levereras långsammare från en viss server. Problem med kombinationer av egenskaperna kan också identifieras som långsamma sidinläsningar i ett geografiskt område för klienter som använder specifika operativsystem.  

Avvikelser som dessa är mycket svårt att identifiera bara genom att granska data, men är vanligare än du tror. Ofta de endast att visa upp när kunderna börjar klaga. Efter den tid det är för sent: de berörda användarna redan byter till dina konkurrenter!

För närvarande kan titta våra algoritmer på sidans belastning, svarstider för begäran på servern och beroende svarstider.  

Du behöver inte ange några tröskelvärden eller konfigurera regler. Machine learning och datautvinningsalgoritmer används för att upptäcka onormala mönster.

![E-postavisering, klicka på länken för att öppna Diagnostikrapporten i Azure](./media/app-insights-proactive-performance-diagnostics/03.png)

* **När** visar den tid som problemet upptäcktes.
* **Vad** beskriver:

  * Problem som har identifierats;
  * Egenskaperna för en uppsättning händelser som vi har hittat visas problemet.
* I tabell jämförs-bristfälligt uppsättningen med det genomsnittliga beteendet för alla andra händelser.

Klicka på länkarna för att öppna Metric Explorer och Sök på relevanta rapporter, filtrerat efter tid och egenskaperna för långsam prestanda uppsättningen.

Ändra tidsintervall och filter för att utforska telemetri.

## <a name="next-steps"></a>Nästa steg
Dessa diagnostiska verktyg hjälper dig att inspektera telemetrin från din app:

* [Profiler](app-insights-profiler.md) 
* [Felsökning av ögonblicksbild](app-insights-snapshot-debugger.md)
* [Analys](app-insights-analytics-tour.md)
* [Analytics smart diagnostik](app-insights-analytics-diagnostics.md)

Smart identifieringar är helt automatisk. Men kanske du vill ställa in några fler aviseringar?

* [Manuellt konfigurerade måttaviseringar](app-insights-alerts.md)
* [Webbtester för tillgänglighet](app-insights-monitor-web-app-availability.md)
