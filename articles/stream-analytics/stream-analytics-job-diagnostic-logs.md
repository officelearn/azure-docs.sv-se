---
title: Felsöka Azure Stream Analytics med hjälp av diagnostikloggar
description: Den här artikeln beskriver hur du analyserar diagnostikloggar i Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/19/2018
ms.custom: seodec18
ms.openlocfilehash: e712dfd755082e6b36066b0058ec18545d5c8417
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412843"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Felsöka Azure Stream Analytics med hjälp av diagnostikloggar

Ibland kan slutar Azure Stream Analytics-jobb oväntat bearbetning. Det är viktigt att kunna felsöka den här typen av händelse. Fel kan orsakas av ett oväntat frågeresultat, anslutning till enheter eller ett oväntat tjänstavbrott. Diagnostikloggar i Stream Analytics kan du identifiera orsaken till problem när de inträffar och minska tiden för återställning.

## <a name="log-types"></a>Loggtyper

Stream Analytics erbjuder två typer av loggar:

* [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (alltid på), som ger insikter om åtgärder som utförs på jobb.

* [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (kan konfigureras), vilket ger bättre insyn i allt som händer med ett jobb. Diagnostik loggar start när jobbet skapas- och när jobbet har tagits bort. De täcker händelser när jobbet har uppdaterats och när den körs.

> [!NOTE]
> Du kan använda tjänster som Azure Storage, Azure Event Hubs och Azure Log Analytics för att analysera avvikande data. Du debiteras enligt priserna för dessa tjänster.

## <a name="debugging-using-activity-logs"></a>Felsöka med hjälp av aktiviteten loggar

Aktivitetsloggar är aktiverade som standard och ge övergripande insikter om åtgärder som utförs av ditt Stream Analytics-jobb. Informationen i aktivitetsloggarna kan hjälpa dig att hitta orsaken till problem som påverkar ditt jobb. Gör följande om du vill använda aktivitetsloggar i Stream Analytics:

1. Logga in på Azure portal och väljer **aktivitetsloggen** under **översikt**.

   ![Stream Analytics aktivitetsloggen](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Du kan se en lista över åtgärder som har utförts. Alla åtgärder som orsakade jobbet misslyckas har en röd info bubbla.

3. Klicka på en åtgärd för att se dess sammanfattningsvyn. Här är ofta begränsad. Om du vill veta mer om igen, klickar du på **JSON**.

   ![Stream Analytics-aktivitet log-åtgärdens sammanfattning](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Rulla ned till den **egenskaper** avsnitt i JSON, vilket ger information om felet som orsakade den misslyckade åtgärden. I det här exemplet var felet på grund av ett körningsfel utanför bundna latitudvärden.

   ![Information om JSON-fel](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Du kan vidta åtgärder utifrån felmeddelandet i JSON. I det här exemplet kontrollerar latitudvärdet är mellan-90 grader och 90 grader måste läggas till i frågan.

6. Om ett felmeddelande i aktivitetsloggarna inte hjälpa dig att identifiera orsaken, aktivera diagnostikloggar och använda Log Analytics.

## <a name="send-diagnostics-to-log-analytics"></a>Skicka diagnostik till Log Analytics

Aktivera diagnostikloggar och skicka dem till Log Analytics rekommenderas starkt. Diagnostikloggar är **av** som standard. Aktivera diagnostikloggar genom att utföra följande steg:

1.  Logga in på Azure Portal och gå till Stream Analytics-jobbet. Under **övervakning**väljer **diagnostikloggar**. Välj sedan **slå på diagnostik**.

    ![Bladnavigering till diagnostikloggar](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Skapa en **namn** i **diagnostikinställningar** och markera kryssrutan bredvid **skicka till Log Analytics**. Lägg till en befintlig eller skapa en ny **Log analytics-arbetsytan**. Markera kryssrutorna för **körning** och **redigering** under **LOG**, och **AllMetrics** under **mått** . Klicka på **Spara**.

    ![Inställningarna för diagnostikloggar](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. När ditt Stream Analytics-jobb startar dirigeras diagnostikloggar till Log Analytics-arbetsytan. Gå till Log Analytics-arbetsytan och välj **loggar** under den **Allmänt** avsnittet.

   ![Log Analytics loggar under Allmänt avsnitt](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. Du kan [skriva en egen fråga](../azure-monitor/log-query/get-started-portal.md) om du vill söka efter termer, identifiera trender, analysera mönster och få insikter utifrån dina data. Exempel: du kan skriva en fråga att filtrera endast diagnostikloggar som har meddelandet ”det direktuppspelade jobbet misslyckades”. Diagnostikloggar från Azure Stream Analytics lagras i den **AzureDiagnostics** tabell.

   ![Diagnostik-fråga och resultat](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. När du har en fråga som söker efter rätt loggarna kan du spara den genom att välja **spara** och ange ett namn och kategori. Du kan sedan skapa en avisering genom att välja **ny aviseringsregel**. Ange sedan aviseringstillståndet. Välj **villkor** och anger tröskelvärdet och frekvensen som den här anpassade loggsökning utvärderas.  

   ![Diagnostiklogg sökfråga](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Välj åtgärdsgruppen och ange detaljerad information om aviseringen, som namn och beskrivning, innan du kan skapa varningsregeln. Du kan dirigera diagnostikloggar för olika jobb till samma Log Analytics-arbetsytan. På så sätt kan du ställa in aviseringar när det fungerar över alla jobb.  

## <a name="diagnostics-log-categories"></a>Diagnostiklogg för kategorier

För närvarande kan vi samla in två typer av diagnostikloggar:

* **Redigera**: Samlar in händelser som är relaterade till jobbet redigering åtgärder, till exempel jobbskapande, att lägga till och ta bort indata och utdata, att lägga till och uppdatera frågan, och startar eller stoppar jobbet.

* **Körning**: Samlar in händelser som inträffar under jobbkörningen.
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
nivå | Loggningsnivå. Till exempel **fel**, **varning**, eller **information**.
properties | Logga post-specifik information om serialiserad som en JSON-sträng. Mer information finns i följande avsnitt i den här artikeln.

### <a name="execution-log-properties-schema"></a>Schema för körning log-egenskaper

Loggarna för jobbkörning har information om händelser som inträffade under jobbkörningen för Stream Analytics. Schemat för egenskaper varierar beroende på vilken typ av händelse. Vi har för närvarande följande typer av loggarna för jobbkörning:

### <a name="data-errors"></a>Datafel

Alla fel som uppstår medan jobbet bearbetar data är i den här kategorin loggar. De här loggarna skapas under data läses, serialisering, oftast och skrivåtgärder. Dessa loggar med inte anslutningsfel. Anslutningsfel behandlas som allmänna händelser.

Namn | Beskrivning
------- | -------
Källa | Namnet på jobbet indata eller utdata där felet uppstod.
Meddelande | Meddelande som associerats med fel.
Typ | Typ av fel. Till exempel **DataConversionError**, **CsvParserError**, eller **ServiceBusPropertyColumnMissingError**.
Data | Innehåller data som är användbar korrekt hitta orsaken till felet. Omfattas av trunkering, beroende på storlek.

Beroende på den **operationName** värde, datafel har följande schema:
* **Serialisera händelser**. Serialisera händelser inträffar när händelser läsåtgärder. De inträffar när data på indata inte uppfyller fråga schemat för något av följande skäl:
    * *Typmatchningsfel när händelser (de) serialisera*: Identifierar det fält som orsakar felet.
    * *Det går inte att läsa en händelse, ogiltig serialisering*: Visar information om plats i indatan där felet uppstod. Innehåller blob-namnet för blob-indata, förskjutning och ett exempel på data.
* **Skicka händelser**. Skicka händelser som inträffar under skrivåtgärder. De identifiera strömningshändelsen som orsakade felet.

### <a name="generic-events"></a>Allmänna händelser

Allmänna händelser täcker allt annat.

Namn | Beskrivning
-------- | --------
Fel | (valfritt) Information om fel. Detta är vanligtvis, undantagsinformation om den är tillgänglig.
Meddelande| Loggmeddelande.
Typ | Typ av meddelande. Mappas till interna kategorisering av fel. Till exempel **JobValidationError** eller **BlobOutputAdapterInitializationFailure**.
Korrelations-ID | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) som unikt identifierar jobbkörningen. Alla körningsloggsposter från tidpunkten då jobbet startar tills jobbet stoppas har samma **Korrelations-ID** värde.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Frågespråksreferens för Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
