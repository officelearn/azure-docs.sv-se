---
title: Felsöka Azure Stream Analytics med hjälp av diagnostikloggar
description: Den här artikeln beskriver hur du analyserar diagnostikloggar i Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: db3c9874676e3240f6896c1e1ff8f873360c20d5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090830"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Felsöka Azure Stream Analytics med hjälp av diagnostikloggar

Ibland kan slutar Azure Stream Analytics-jobb oväntat bearbetning. Det är viktigt för att kunna felsöka den här typen av händelse. Händelsen kan orsakas av ett oväntat frågeresultat, anslutning till enheter eller ett oväntat tjänstavbrott. Diagnostikloggar i Stream Analytics kan du identifiera orsaken till problem när de inträffar och minska tiden för återställning.

## <a name="log-types"></a>Loggtyper

Stream Analytics erbjuder två typer av loggar: 
* [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (alltid på). Aktivitetsloggar ger insikt i åtgärder som utförs på jobb.
* [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (kan konfigureras). Diagnostikloggar ger bättre insyn i allt som händer med ett jobb. Diagnostik loggar start när jobbet skapas- och slutdatum när jobbet har tagits bort. De täcker händelser när jobbet har uppdaterats och när den körs.

> [!NOTE]
> Du kan använda tjänster som Azure Storage, Azure Event Hubs och Azure Log Analytics för att analysera avvikande data. Du debiteras enligt priserna för dessa tjänster.
>

## <a name="turn-on-diagnostics-logs"></a>Aktivera diagnostikloggar

Diagnostikloggar är **av** som standard. Aktivera diagnostikloggar genom att utföra följande steg:

1.  Logga in på Azure-portalen och gå till bladet jobb för direktuppspelning. Under **övervakning**väljer **diagnostikloggar**.

    ![Bladnavigering till diagnostikloggar](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Välj **slå på diagnostik**.

    ![Aktivera diagnostikloggar för Stream Analytics](./media/stream-analytics-job-diagnostic-logs/turn-on-diagnostic-logs.png)

3.  På den **diagnostikinställningar** sidan för **Status**väljer **på**.

    ![Ändra status för diagnostikloggar](./media/stream-analytics-job-diagnostic-logs/save-diagnostic-log-settings.png)

4.  Ställ in arkivering målet (storage-konto, händelsehubb, Log Analytics) som du vill. Välj kategorier av loggar som du vill samla in (körning, redigering). 

5.  Spara den nya diagnostikkonfigurationen.

Diagnostikkonfigurationen tar cirka 10 minuter ska börja gälla. Efter det börjar loggarna visas i konfigurerade mål-arkivering (du kan se dessa på den **diagnostikloggar** sidan):

![Bladnavigering till diagnostikloggar - arkivering mål](./media/stream-analytics-job-diagnostic-logs/view-diagnostic-logs-page.png)

Läs mer om hur du konfigurerar diagnostik [samla in och använda diagnostikdata från dina Azure-resurser](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Diagnostiklogg för kategorier

För närvarande kan vi samla in två typer av diagnostikloggar:

* **Redigera**. Insamlingar logga händelser som är relaterade till jobbet redigering operations: skapa, lägga till och ta bort indata och utdata, lägga till och uppdatera frågan, starta och stoppa jobbet jobb.
* **Körning**. Samlar in händelser som inträffar under jobbkörningen:
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
properties | Logga post-specifik information om serialiserad som en JSON-sträng. Mer information finns i följande avsnitt.

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
    * *Typmatchningsfel när händelser (de) serialisera*: identifierar fältet som orsakar felet.
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
