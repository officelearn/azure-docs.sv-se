---
title: Felsöka Azure Stream Analytics med hjälp av diagnostikloggar
description: I den här artikeln beskrivs hur du analyserar diagnostikloggar i Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: cdb6629441becd0a8356debe3360830ff11a7a9d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398411"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Felsöka Azure Stream Analytics med hjälp av diagnostikloggar

Ibland kan ett Azure Stream Analytics-jobb oväntat avbryta bearbetningen. Det är viktigt att kunna felsöka den här typen av händelse. Fel kan inträffa på grund av ett oväntat frågeresultat, enhetsanslutningar eller ett oväntat tjänstavbrott. Diagnostikloggarna i Stream Analytics kan hjälpa dig att identifiera orsaken till problem när de uppstår och minska återställningstiden.

Det rekommenderas starkt att aktivera diagnostiska loggar för alla jobb eftersom detta kommer att i hög grad hjälpa till med felsökning och övervakning.

## <a name="log-types"></a>Loggtyper

Stream Analytics erbjuder två typer av loggar:

* [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (alltid på), som ger insikter om åtgärder som utförs på jobb.

* [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurerbara), som ger rikare insikter i allt som händer med ett jobb. Diagnostikloggar startar när jobbet skapas och avslutas när jobbet tas bort. De täcker händelser när jobbet uppdateras och medan det körs.

> [!NOTE]
> Du kan använda tjänster som Azure Storage, Azure Event Hubs och Azure Monitor-loggar för att analysera data som inte tärtar. Du debiteras baserat på prismodellen för dessa tjänster.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Felsöka med aktivitetsloggar

Aktivitetsloggar är aktiverade som standard och ger insikter på hög nivå om åtgärder som utförs av ditt Stream Analytics-jobb. Information som finns i aktivitetsloggar kan hjälpa till att hitta orsaken till de problem som påverkar ditt jobb. Gör så här för att använda aktivitetsloggar i Stream Analytics:

1. Logga in på Azure-portalen och välj **Aktivitetslogg** under **Översikt**.

   ![Aktivitetslogg för Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Du kan se en lista över åtgärder som har utförts. Alla åtgärder som orsakade ditt jobb att misslyckas har en röd info bubbla.

3. Klicka på en åtgärd om du vill visa sammanfattningsvyn. Informationen här är ofta begränsad. Om du vill veta mer om åtgärden klickar du på **JSON**.

   ![Sammanfattning av aktivitetsloggen för Stream Analytics-aktivitetslogg](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Bläddra ned till avsnittet **Egenskaper** i JSON, som innehåller information om felet som orsakade den misslyckade åtgärden. I det här exemplet berodde felet på ett körningsfel från värden som inte är bundna. Om du bortser från data som bearbetas av ett Stream Analytics-jobb orsakar ett datafel. Du kan läsa om olika [in- och utdatafel och varför de uppstår](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![JSON-felinformation](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Du kan vidta korrigerande åtgärder baserat på felmeddelandet i JSON. I det här exemplet måste kontroller för att säkerställa att latitudvärdet ligger mellan -90 grader och 90 grader läggas till i frågan.

6. Om felmeddelandet i aktivitetsloggarna inte är till hjälp för att identifiera grundorsaken aktiverar du diagnostikloggar och använder Azure Monitor-loggar.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Skicka diagnostik till Azure Monitor-loggar

Det rekommenderas starkt att aktivera diagnostikloggar och skicka dem till Azure Monitor-loggar. Diagnostikloggar är **inaktiverade** som standard. Så här aktiverar du diagnostikloggar:

1.  Logga in på Azure-portalen och navigera till ditt Stream Analytics-jobb. Under **Övervakning**väljer du **Diagnostikloggar**. Välj sedan **Aktivera diagnostik**.

    ![Bladnavigering till diagnostikloggar](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Skapa ett **namn** i **diagnostikinställningar och** markera kryssrutan **bredvid Skicka till Logganalys**. Lägg sedan till en befintlig eller skapa en ny **logganalysarbetsyta**. Markera rutorna för **körning** och **redigering** under **LOG**och **AllMetrics** under **METRIC**. Klicka på **Spara**. Vi rekommenderar att du använder en Log Analytics-arbetsyta i samma Azure-region som ditt Stream Analytics-jobb för att förhindra ytterligare kostnader.

    ![Inställningar för diagnostikloggar](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. När stream analytics-jobbet startar dirigeras diagnostikloggar till logganalysarbetsytan. Om du vill visa diagnostikloggar för jobbet väljer du **Loggar** under avsnittet **Övervakning.**

   ![Diagnostiska loggar under övervakning](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics innehåller fördefinierade frågor som gör att du enkelt kan söka efter de loggar som du är intresserad av. De tre kategorierna är **Allmänna,** **Indatafel** och **Utdatafel**. Om du till exempel vill visa en sammanfattning av alla fel i ditt jobb under de senaste 7 dagarna kan du välja **Kör** av lämplig fördefinierad fråga. 

   ![Diagnostiska loggar under övervakning](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Resultat av loggar](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Loggkategorier för diagnostiklogg

Azure Stream Analytics samlar in två kategorier av diagnostikloggar:

* **Redigering**: Samlar in logghändelser som är relaterade till jobbförfattande åtgärder, till exempel jobbskapande, lägga till och ta bort indata och utdata, lägga till och uppdatera frågan och starta eller stoppa jobbet.

* **Körning**: Fångar händelser som inträffar under jobbkörning.
    * Anslutningsfel
    * Databehandlingsfel, inklusive:
        * Händelser som inte överensstämmer med frågedefinitionen (fälttyper och värden som inte stämmer överens, fält som saknas och så vidare)
        * Fel i utvärdering av uttryck
    * Andra händelser och fel

## <a name="diagnostics-logs-schema"></a>Schema för diagnostikloggar

Alla loggar lagras i JSON-format. Varje post har följande vanliga strängfält:

Namn | Beskrivning
------- | -------
time | Tidsstämpel (i UTC) för loggen.
resourceId | ID för den resurs som åtgärden genomfördes på, i versaler. Den innehåller prenumerations-ID, resursgruppen och projektnamnet. Till exempel **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Loggkategori, antingen **Utförande** eller **Redigering**.
operationName | Namnet på den åtgärd som loggas. **Skicka händelser: SKRIVFEL för SQL-utdata till mysqloutput**.
status | Status för operationen. Misslyckades **eller** **lyckades**till exempel .
nivå | Loggnivå. Fel, **Error** **Varning**eller **Informationsriktelse**.
properties | Logginmatningsspecifik detalj, serialiserad som en JSON-sträng. Mer information finns i följande avsnitt i den här artikeln.

### <a name="execution-log-properties-schema"></a>Egenskapsschema för körningslogg

Körningsloggar har information om händelser som inträffade under körning av Stream Analytics-jobb. Schemat för egenskaper varierar beroende på om händelsen är ett datafel eller en allmän händelse.

### <a name="data-errors"></a>Datafel

Alla fel som uppstår när jobbet bearbetar data finns i den här kategorin av loggar. Dessa loggar skapas oftast under dataläsning, serialisering och skrivåtgärder. Dessa loggar innehåller inte anslutningsfel. Anslutningsfel behandlas som allmänna händelser. Du kan läsa mer om orsaken till olika [indata- och utdatafel](https://docs.microsoft.com/azure/stream-analytics/data-errors).

Namn | Beskrivning
------- | -------
Källa | Namnet på projektindata eller utdata där felet uppstod.
Meddelande | Meddelande som är associerat med felet.
Typ | Typ av fel. **DataConversionError**, **CsvParserError**eller **ServiceBusPropertyColumnMissingError**.
Data | Innehåller data som är användbara för att korrekt hitta källan till felet. Med förbehåll för trunkering, beroende på storlek.

Beroende på **värdet operationName** har datafel följande schema:

* **Serialisera händelser** inträffar under händelseläsningsåtgärder. De uppstår när data vid indata inte uppfyller frågeschemat av något av följande skäl:

   * *Skriv in felmatchning under händelse (de)serialisera*: Identifierar det fält som orsakar felet.

   * *Det går inte att läsa en händelse, ogiltig serialisering:* Visar information om platsen i indata där felet uppstod. Innehåller blob-namn för blob-indata, förskjutning och ett exempel på data.

* **Skicka händelser** inträffar under skrivåtgärder. De identifierar den direktuppspelningshändelse som orsakade felet.

### <a name="generic-events"></a>Allmänna händelser

Generiska händelser täcker allt annat.

Namn | Beskrivning
-------- | --------
Fel | (valfritt) Felinformation. Detta är vanligtvis undantagsinformation om den är tillgänglig.
Meddelande| Logga meddelande.
Typ | Typ av meddelande. Kartor till intern kategorisering av fel. Till exempel **JobValidationError** eller **BlobOutputAdapterInitializationFailure**.
Korrelations-ID | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) som unikt identifierar jobbkörningen. Alla körningsloggtransaktioner från det att jobbet startar tills jobbet stoppas har samma **korrelations-ID-värde.**

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Webbplatsreferens för Stream Analytics-frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Datafel i Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-errors)
