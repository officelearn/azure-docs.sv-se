---
title: Felsöka Azure Stream Analytics med hjälp av diagnostikloggar
description: Den här artikeln beskriver hur du analyserar diagnostikloggar i Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: f318b373f6a6f46ee3a85703c6099c76568580ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426162"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Felsöka Azure Stream Analytics med hjälp av diagnostikloggar

Ibland kan ett Azure Stream Analytics-jobb oväntat avbryta bearbetningen. Det är viktigt att kunna felsöka den här typen av händelse. Fel kan inträffa på grund av ett oväntat frågeresultat, enhetsanslutningar eller ett oväntat tjänstavbrott. Diagnostikloggar i Stream Analytics kan hjälpa dig att identifiera orsaken till problem när de inträffar och minska återställnings tiden.

Vi rekommenderar starkt att du aktiverar diagnostikloggar för alla jobb eftersom detta kommer att vara mycket hjälp med fel sökning och övervakning.

## <a name="log-types"></a>Loggtyper

Stream Analytics erbjuder två typer av loggar:

* [Aktivitets loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (alltid på), vilket ger insikter om åtgärder som utförs på jobb.

* [Diagnostikloggar (kan](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) konfigureras), vilket ger bättre insikter om allt som händer med ett jobb. Diagnostikloggar startar när jobbet skapas och avslutas när jobbet tas bort. De täcker händelser när jobbet har uppdaterats och när den körs.

> [!NOTE]
> Du kan använda tjänster som Azure Storage, Azure Event Hubs och Azure Monitor loggar för att analysera data som inte överensstämmer. Du debiteras enligt priserna för dessa tjänster.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Felsöka med aktivitets loggar

Aktivitets loggarna är aktiverat som standard och ger höga insikter om åtgärder som utförs av ditt Stream Analytics jobb. Information som finns i aktivitets loggar kan hjälpa dig att hitta rotor saken till problem som påverkar ditt jobb. Utför följande steg för att använda aktivitets loggar i Stream Analytics:

1. Logga in på Azure Portal och välj **aktivitets logg** under **Översikt**.

   ![Stream Analytics aktivitets logg](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Du kan se en lista över åtgärder som har utförts. Alla åtgärder som gjorde att jobbet misslyckades har en röd informations bubbla.

3. Klicka på en åtgärd för att visa dess sammanfattningsvy. Informationen här är ofta begränsad. Om du vill ha mer information om åtgärden klickar du på **JSON**.

   ![Sammanfattning av Stream Analytics aktivitets loggs åtgärd](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Rulla ned till avsnittet **Egenskaper** i JSON, där det finns information om felet som orsakade åtgärden. I det här exemplet berodde felet på ett körnings fel från de kopplade Latitude-värdena. En avvikelse i data som bearbetas av ett Stream Analytics jobb orsakar ett data fel. Du kan lära dig mer om olika [indata och fel i utdata och varför de inträffar](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![JSON-fel information](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Du kan vidta korrigerande åtgärder baserat på fel meddelandet i JSON. I det här exemplet, kontrollerar att värdet på latitud är mellan-90 grader och 90 grader måste läggas till i frågan.

6. Om fel meddelandet i aktivitets loggarna inte är till hjälp när du identifierar rotor saken, aktiverar du diagnostikloggar och använder Azure Monitor loggar.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Skicka diagnostik till Azure Monitor loggar

Det rekommenderas starkt att du aktiverar diagnostikloggar och skickar dem till Azure Monitor loggar. Diagnostikloggar är **av** som standard. Aktivera diagnostikloggar genom att utföra följande steg:

1.  Logga in på Azure Portal och navigera till ditt Stream Analytics-jobb. Under **övervakning**väljer **diagnostikloggar**. Välj sedan **Aktivera diagnostik**.

    ![Bladnavigering till diagnostikloggar](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Skapa ett **namn** i **diagnostikinställningar** och markera kryss rutan bredvid **Skicka till Log Analytics**. Lägg sedan till en befintlig eller skapa en ny **Log Analytics-arbetsyta**. Markera kryss rutorna för **körning** och **redigering** under **logg**och **AllMetrics** under **mått**. Klicka på **Spara**. Vi rekommenderar att du använder en Log Analytics arbets yta i samma Azure-region som ditt Stream Analytics jobb för att förhindra ytterligare kostnader.

    ![Inställningar för diagnostikloggar](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. När ditt Stream Analytics jobb startar dirigeras diagnostikloggar till Log Analytics-arbetsytan. Om du vill visa diagnostikloggar för ditt jobb väljer du **loggar** i avsnittet **övervakning** .

   ![Diagnostikloggar under övervakning](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics innehåller fördefinierade frågor som gör det enkelt att söka efter de loggar som du är intresse rad av. De tre kategorierna är **allmänna**, **indata-fel** och datafel för **utdata**. Om du till exempel vill visa en sammanfattning av alla fel i jobbet under de senaste 7 dagarna kan du välja att **köra** rätt fördefinierad fråga. 

   ![Diagnostikloggar under övervakning](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Resultat från loggar](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Diagnostiklogg för kategorier

Azure Stream Analytics fångar två typer av diagnostikloggar:

* **Redigering**: fångar logg händelser som är relaterade till jobb redigerings åtgärder, till exempel skapande av jobb, lägga till och ta bort indata och utdata, lägga till och uppdatera frågan och starta eller stoppa jobbet.

* **Körning**: fångar händelser som inträffar under jobb körningen.
    * Anslutningsfel
    * Bearbetning av fel, inklusive:
        * Händelser som inte överensstämmer med frågedefinitionen (Felmatchade fälttyper och värden, saknade fält och så vidare)
        * Fel för utvärdering av uttryck
    * Andra händelser och fel

## <a name="diagnostics-logs-schema"></a>Schema för Diagnostics-loggar

Alla loggar lagras i JSON-format. Varje post innehåller följande sträng fälten:

Namn | Beskrivning
------- | -------
time | Tidsstämpel (i UTC) i loggen.
resourceId | ID för den resurs som att åtgärden ägde rum, i versaler. Det omfattar prenumerations-ID, resursgruppen och namnet på jobb. Till exempel   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Logga kategori, antingen **körning** eller **redigering**.
operationName | Namnet på åtgärden som loggas. Till exempel **skicka händelser: SQL-utdata skriva fel till mysqloutput**.
status | Status för åtgärden. Till exempel **misslyckades** eller **lyckades**.
level | Loggningsnivå. Till exempel **fel**, **varning**, eller **information**.
properties | Logga post-specifik information om serialiserad som en JSON-sträng. Mer information finns i följande avsnitt i den här artikeln.

### <a name="execution-log-properties-schema"></a>Schema för körning log-egenskaper

Loggarna för jobbkörning har information om händelser som inträffade under jobbkörningen för Stream Analytics. Schemat för egenskaper varierar beroende på om händelsen är ett data fel eller en allmän händelse.

### <a name="data-errors"></a>Datafel

Alla fel som uppstår medan jobbet bearbetar data är i den här kategorin loggar. De här loggarna skapas under data läses, serialisering, oftast och skrivåtgärder. Dessa loggar med inte anslutningsfel. Anslutningsfel behandlas som allmänna händelser. Du kan lära dig mer om orsaken till olika [fel i indata och utdata](https://docs.microsoft.com/azure/stream-analytics/data-errors).

Namn | Beskrivning
------- | -------
Källa | Namnet på jobbet indata eller utdata där felet uppstod.
Meddelande | Meddelande som associerats med fel.
Typ | Typ av fel. Till exempel **DataConversionError**, **CsvParserError**, eller **ServiceBusPropertyColumnMissingError**.
Data | Innehåller data som är användbar korrekt hitta orsaken till felet. Omfattas av trunkering, beroende på storlek.

Beroende på den **operationName** värde, datafel har följande schema:

* **Serialisering av händelser** sker under händelse Läs åtgärder. De inträffar när data på indata inte uppfyller fråga schemat för något av följande skäl:

   * *Typmatchningsfel när händelser (de) serialisera*: identifierar fältet som orsakar felet.

   * *Det går inte att läsa en händelse, ogiltig serialisering*: Visar information om plats i indatan där felet uppstod. Innehåller blob-namnet för blob-indata, förskjutning och ett exempel på data.

* **Sändnings händelser** inträffar under Skriv åtgärder. De identifiera strömningshändelsen som orsakade felet.

### <a name="generic-events"></a>Allmänna händelser

Allmänna händelser täcker allt annat.

Namn | Beskrivning
-------- | --------
Fel | (valfritt) Information om fel. Detta är vanligt vis undantags information om det är tillgängligt.
Meddelande| Loggmeddelande.
Typ | Typ av meddelande. Mappas till interna kategorisering av fel. Till exempel **JobValidationError** eller **BlobOutputAdapterInitializationFailure**.
Korrelations-ID | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) som unikt identifierar jobbkörningen. Alla körningsloggsposter från tidpunkten då jobbet startar tills jobbet stoppas har samma **Korrelations-ID** värde.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Frågespråksreferens för Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics data fel](https://docs.microsoft.com/azure/stream-analytics/data-errors)
