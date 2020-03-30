---
title: Smart identifiering – prestandaavvikelser | Microsoft-dokument
description: Application Insights utför smart analys av din apptelemetri och varnar dig för potentiella problem. Den här funktionen behöver ingen installation.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: 3d8de08605d3dd693eb74a84a29c2efa6cad669a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671740"
---
# <a name="smart-detection---performance-anomalies"></a>Smart identifiering - Prestandaavvikelser

[Application Insights](../../azure-monitor/app/app-insights-overview.md) analyserar automatiskt webbprogrammets prestanda och kan varna dig om potentiella problem. Du kanske läser detta eftersom du har fått en av våra meddelanden om smart identifiering.

Den här funktionen kräver ingen särskild konfiguration, förutom att konfigurera appen för Application Insights (på [ASP.NET,](../../azure-monitor/app/asp-net.md) [Java](../../azure-monitor/app/java-get-started.md)eller [Node.js](../../azure-monitor/app/nodejs.md)och i [webbsidans kod](../../azure-monitor/app/javascript.md)). Den är aktiv när appen genererar tillräckligt med telemetri.

## <a name="when-would-i-get-a-smart-detection-notification"></a>När får jag en anmälan om smart identifiering?

Application Insights har upptäckt att programmets prestanda har försämrats på något av följande sätt:

* **Svarstidsförsämring** – Appen har börjat svara på begäranden långsammare än tidigare. Ändringen kan ha varit snabb, till exempel eftersom det fanns en regression i den senaste distributionen. Eller det kan ha varit gradvis, kanske orsakas av ett minne läcka. 
* **Försämring av beroendevaraktighet** - Appen anropar ett REST API, en databas eller något annat beroende. Beroendet svarar långsammare än tidigare.
* **Långsamt prestandamönster** – Appen verkar ha ett prestandaproblem som bara påverkar vissa begäranden. Till exempel läses sidor långsammare på en typ av webbläsare än andra. eller förfrågningar serveras långsammare från en viss server. För närvarande tittar våra algoritmer på sidans inläsningstider, svarstider för begäran och beroendesvarstider.  

Smart identifiering kräver minst 8 dagars telemetri med en fungerande volym för att fastställa en baslinje för normal prestanda. Så, efter att ditt program har körts under den perioden, kommer alla betydande problem att resultera i ett meddelande.


## <a name="does-my-app-definitely-have-a-problem"></a>Har min app definitivt ett problem?

Nej, ett meddelande betyder inte att din app definitivt har ett problem. Identifieringen är bara ett förslag på något som du kanske vill titta närmare på.

## <a name="how-do-i-fix-it"></a>Vad kan jag göra?

Meddelandena innehåller diagnostikinformation. Här är ett exempel:


![Här är ett exempel på identifiering av serversvarstidsförsämring](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Triage**. Meddelandet visar hur många användare eller hur många åtgärder som påverkas. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattning**. Påverkar problemet all trafik, eller bara några sidor? Är det begränsat till vissa webbläsare eller platser? Denna information kan erhållas från anmälan.
3. **Diagnostisera**. Ofta tyder den diagnostiska informationen i anmälan på problemets natur. Om svarstiden till exempel saktar ned när begäranden är hög, tyder det på att servern eller beroenden överbelastas. 

    Annars öppnar du bladet Prestanda i Application Insights. Där hittar du [Profiler data.](profiler.md) Om undantag genereras kan du också prova [felsökaren](../../azure-monitor/app/snapshot-debugger.md)för ögonblicksbilder .



## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Smarta identifieringsmeddelanden aktiveras som standard och skickas till dem som har åtkomst till [övervakningsläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) och [övervakningsbidragstagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) till prenumerationen där application insights-resursen finns. Om du vill ändra detta klickar du antingen på **Konfigurera** i e-postmeddelandet eller öppnar inställningar för smart identifiering i Application Insights. 
  
  ![Inställningar för smart identifiering](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Du kan använda länken **för att avsluta prenumerationen** i e-postmeddelandet med smart identifiering för att sluta ta emot e-postmeddelandena.

E-postmeddelanden om prestandaavvikelser för smarta identifieringar är begränsade till ett e-postmeddelande per dag per Application Insights-resurs. E-postmeddelandet skickas endast om det finns minst ett nytt problem som upptäcktes den dagen. Du kommer inte att få upprepningar av något meddelande. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

* *Så, Microsoft-personal titta på mina data?*
  * Nej. Tjänsten är helt automatisk. Bara du får aviseringarna. Dina data är [privata](../../azure-monitor/app/data-retention-privacy.md).
* *Analyserar du alla data som samlas in av Application Insights?*
  * Inte för närvarande. För närvarande analyserar vi svarstid för begäran, beroendesvartid och sidans inläsningstid. Analys av ytterligare mått är på vår eftersläpning ser fram emot.

* Vilka typer av program fungerar detta för?
  * Dessa nedbrytningar identifieras i alla program som genererar lämplig telemetri. Om du har installerat Application Insights i webbappen spåras begäranden och beroenden automatiskt. Men i backend-tjänster eller andra appar, om du infogade samtal till [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) eller [TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency), då Smart Detection kommer att fungera på samma sätt.

* *Kan jag skapa mina egna avvikelseidentifieringsregler eller anpassa befintliga regler?*

  * Inte än, men du kan:
    * [Ställ in aviseringar](../../azure-monitor/app/alerts.md) som talar om för dig när ett mått överskrider ett tröskelvärde.
    * [Exportera telemetri](../../azure-monitor/app/export-telemetry.md) till en [databas](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) eller [till PowerBI](../../azure-monitor/app/export-power-bi.md ), där du kan analysera den själv.
* *Hur ofta utförs analysen?*

  * Vi kör analysen dagligen på telemetrin från föregående dag (hel dag i UTC tidszon).
* *Så ersätter detta [måttvarningar?](../../azure-monitor/app/alerts.md)*
  * Nej.  Vi förbinder oss inte att upptäcka alla beteenden som du kan anse vara onormala.


* *Om jag inte gör något som svar på en avisering, får jag en påminnelse?*
  * Nej, du får ett meddelande om varje problem bara en gång. Om problemet kvarstår uppdateras det i matningsbladet för smart identifiering.
* *Jag förlorade e-postmeddelandet. Var hittar jag aviseringarna i portalen?*
  * Klicka på panelen **Smart identifiering** i översikten Programstatistik för din app. Där kan du hitta alla aviseringar för upp till 90 dagar sedan.

## <a name="how-can-i-improve-performance"></a>Hur kan jag förbättra prestanda?
Långsamma och misslyckade svar är en av de största frustrationerna för webbplatsanvändare, som ni vet från din egen erfarenhet. Så det är viktigt att ta itu med frågorna.

### <a name="triage"></a>Triage
För det första, spelar det någon roll? Om en sida är alltid långsam att ladda, men bara 1% av webbplatsens användare någonsin måste titta på det, kanske du har viktigare saker att tänka på. Å andra sidan, om bara 1% av användarna öppnar den, men det kastar undantag varje gång, som kan vara värt att undersöka.

Använd konsekvensbeskrivningen (berörda användare eller % av trafiken) som en allmän guide, men tänk på att det inte är hela artikeln. Samla in andra bevis för att bekräfta.

Tänk på parametrarna för problemet. Om det är geografiberoende ställer du in [tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md) inklusive den regionen: det kan helt enkelt finnas nätverksproblem i det området.

### <a name="diagnose-slow-page-loads"></a>Diagnostisera långsamma sidläsningar
Var är problemet? Är servern långsam att svara, är sidan mycket lång, eller har webbläsaren måste göra en hel del arbete för att visa den?

Öppna metriska bladet Webbläsare. Den segmenterade visningen av inläsningstiden för webbläsarsidan visar vart tiden är på väg. 

* Om **Skicka begärandetid** är hög svarar antingen servern långsamt eller så är begäran ett inlägg med mycket data. Titta på [prestandamåtten](../../azure-monitor/app/web-monitor-performance.md#metrics) för att undersöka svarstider.
* Ställ in [beroendespårning](../../azure-monitor/app/asp-net-dependencies.md) för att se om långsamheten beror på externa tjänster eller databasen.
* Om **mottagande svar** är dominerande, din sida och dess beroende delar - JavaScript, CSS, bilder och så vidare (men inte asynkront inlästa data) är långa. Ställ in ett [tillgänglighetstest](../../azure-monitor/app/monitor-web-app-availability.md)och se till att ange alternativet för att läsa in beroende delar. När du får några resultat öppnar du detaljerna i ett resultat och expanderar det för att se laddningstiderna för olika filer.
* Hög **klientbearbetningstid** antyder att skript körs långsamt. Om orsaken inte är uppenbar kan du överväga att lägga till viss tidskod och skicka tiderna i trackMetric-anrop.

### <a name="improve-slow-pages"></a>Förbättra långsamma sidor
Det finns ett nät fullt av råd om hur du förbättrar dina serversvar och sidans laddningstider, så vi kommer inte att försöka upprepa allt här. Här är några tips som du förmodligen redan vet om, bara för att få dig att tänka:

* Långsam inläsning på grund av stora filer: Läs in skript och andra delar asynkront. Använd skriptkombinationserbjudanden. Dela upp huvudsidan i widgetar som läser in deras data separat. Skicka inte vanlig gammal HTML för långa tabeller: använd ett skript för att begära data som JSON eller annat kompakt format och fyll sedan tabellen på plats. Det finns stora ramar för att hjälpa till med allt detta. (De innebär också stora skript, naturligtvis.)
* Långsamma serverberoenden: Tänk på komponenternas geografiska platser. Om du till exempel använder Azure kontrollerar du att webbservern och databasen finns i samma region. Hämtar frågor mer information än de behöver? Skulle cachelagring eller batchning hjälpa?
* Kapacitetsproblem: Titta på servermåtten för svarstider och antal begäranden. Om svarstiderna når en oproportionerlig topp med toppar i antal begäranden är det troligt att servrarna är utsträckta.


## <a name="server-response-time-degradation"></a>Försämring av serversvarstid

Meddelandet om försämring av svarstiden visar:

* Svarstiden jämfört med normal svarstid för den här åtgärden.
* Hur många användare påverkas.
* Genomsnittlig svarstid och 90:e percentilens svarstid för den här åtgärden på dagen för identifieringen och 7 dagar före. 
* Antal av den här åtgärdens begäranden på dagen för identifieringen och 7 dagar före.
* Korrelation mellan nedbrytning i den här åtgärden och nedbrytningar i relaterade beroenden. 
* Länkar som hjälper dig att diagnostisera problemet.
  * Profilerspårningar som hjälper dig att visa var operationstiden spenderas (länken är tillgänglig om Profiler-spårningsexempel samlades in för den här åtgärden under identifieringsperioden). 
  * Prestandarapporter i Metric Explorer, där du kan segmentera och tärna tidsintervall/filter för den här åtgärden.
  * Sök efter det här anropet om du vill visa specifika samtalsegenskaper.
  * Felrapporter - Om antalet > 1 innebär det att det fanns fel i den här åtgärden som kan ha bidragit till prestandaförsämring.

## <a name="dependency-duration-degradation"></a>Försämring av beroendevaraktighet

Modern tillämpning mer och mer anta mikrotjänster design strategi, vilket i många fall leder till tung tillförlitlighet på externa tjänster. Till exempel, om ditt program förlitar sig på vissa dataplattform eller ens om du bygger din egen bot tjänst kommer du förmodligen vidarebefordra på vissa kognitiva tjänsteleverantörer för att göra det möjligt för dina robotar att interagera på mer mänskliga sätt och vissa datalager tjänst för bot att dra svaren Från.  

Exempel på meddelande om försämring av beroendeberoende:

![Här är ett exempel på identifiering av försämring av beroendevaraktighet](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Observera att det säger dig:

* Varaktigheten jämfört med normal svarstid för den här åtgärden
* Hur många användare påverkas
* Genomsnittlig varaktighet och 90:e percentilen för detta beroende på dagen för identifieringen och 7 dagar före
* Antal beroendeanrop på dagen för identifieringen och 7 dagar före
* Länkar som hjälper dig att diagnostisera problemet
  * Prestandarapporter i Metric Explorer för det här beroendet
  * Sök efter det här beroendeanropet för att visa samtalsegenskaper
  * Felrapporter - Om antalet > 1 innebär det att det inte gick att anropa beroendesamtal under identifieringsperioden som kan ha bidragit till varaktighetsförsämring. 
  * Öppna Analytics med frågor som beräknar den här beroendevaraktigheten och antalet  

## <a name="smart-detection-of-slow-performing-patterns"></a>Smart identifiering av långsamt presterande mönster 

Application Insights hittar prestandaproblem som bara kan påverka en del av användarna eller bara påverka användare i vissa fall. Till exempel är meddelanden om sidor inläsning långsammare på en typ av webbläsare än på andra typer av webbläsare, eller om begäranden visas långsammare från en viss server. Den kan också upptäcka problem i samband med kombinationer av egenskaper, till exempel långsamma sidbelastningar i ett geografiskt område för klienter som använder ett visst operativsystem.  

Avvikelser som dessa är mycket svåra att upptäcka bara genom att inspektera data, men är vanligare än du kanske tror. Ofta de bara yta när dina kunder klagar. Vid det laget är det för sent: de drabbade användarna redan byter till dina konkurrenter!

För närvarande tittar våra algoritmer på sidans laddningstider, begärandesvarstider på servern och beroendesvarstider.  

Du behöver inte ange några tröskelvärden eller konfigurera regler. Maskininlärning och datautvinningsalgoritmer används för att identifiera onormala mönster.

![Från e-postaviseringen klickar du på länken för att öppna diagnostikrapporten i Azure](./media/proactive-performance-diagnostics/03.png)

* **När** visar den tid då problemet upptäcktes.
* **Vad** beskriver:

  * Problemet som upptäcktes;
  * Egenskaperna för den uppsättning händelser som vi hittade visade problemet beteende.
* Tabellen jämför den dåligt presterande uppsättningen med det genomsnittliga beteendet för alla andra händelser.

Klicka på länkarna för att öppna Metric Explorer och Sök i relevanta rapporter, filtrerade efter tid och egenskaper för den långsamma uppsättningen.

Ändra tidsintervallet och filtren för att utforska telemetrin.

## <a name="next-steps"></a>Nästa steg
De här diagnostikverktygen hjälper dig att granska telemetrin från din app:

* [Profiler](profiler.md) 
* [Felsökning av ögonblicksbild](../../azure-monitor/app/snapshot-debugger.md)
* [Analys](../../azure-monitor/log-query/get-started-portal.md)
* [Analysera smart diagnostik](../../azure-monitor/app/analytics.md)

Smarta identifieringar är helt automatiska. Men du kanske vill sätta upp fler varningar?

* [Manuellt konfigurerade måttaviseringar](../../azure-monitor/app/alerts.md)
* [Webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md)
