---
title: "Felsöka Azure Stream Analytics med diagnostik loggar | Microsoft Docs"
description: "Lär dig att analysera diagnostik loggar från Stream Analytics-jobb i Microsoft Azure."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: c9772df2c216d465ca6e90e69bce011969dd4f02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Felsöka Azure Stream Analytics med hjälp av diagnostik-loggar

Ibland kan slutar en Azure Stream Analytics-jobbet oväntat bearbetning. Det är viktigt för att kunna felsöka den här typen av händelse. Händelsen kan orsakas av ett oväntat frågeresultatet, av anslutning till enheter eller av ett oväntat avbrott. Diagnostik-loggar i Stream Analytics hjälper du reda på orsaken till problem när de inträffar och minska tiden för återställning.

## <a name="log-types"></a>Log-typer

Stream Analytics finns två typer av loggar: 
* [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (alltid på). Aktivitetsloggar ge insikter om åtgärder som utförs på jobb.
* [Diagnostik loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigureras). Diagnostik-loggarna ger bättre insikter om allt som händer med ett jobb. Diagnostik loggar start när jobbet har skapats och slutar när jobbet har tagits bort. De täcker händelser när jobbet har uppdaterats och när den körs.

> [!NOTE]
> Du kan använda tjänster som Azure Storage, Azure Event Hubs och Azure Log Analytics för att analysera avvikande data. Du debiteras baserat på priserna för dessa tjänster.
>

## <a name="turn-on-diagnostics-logs"></a>Aktivera diagnostik loggar

Diagnostik loggar är **av** som standard. Aktivera diagnostik loggarna genom att utföra följande steg:

1.  Logga in på Azure portal och gå till bladet strömmande jobb. Under **övervakning**väljer **diagnostik loggar**.

    ![Bladet navigering till diagnostik loggar](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  Välj **aktivera diagnostiken**.

    ![Aktivera diagnostik loggar](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  På den **diagnostikinställningarna** sidan för **Status**väljer **på**.

    ![Ändra status för diagnostik-loggar](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  Ställ in arkivering mål (storage-konto, händelsehubb, logganalys) som du vill. Markera kategorierna av loggar som du vill samla in (körning, redigering). 

5.  Spara den nya konfigurationen diagnostik.

Hur diagnostik tar cirka 10 minuter ska börja gälla. Efter att starta loggarna visas i konfigurerade mål-arkivering (du kan se dessa på den **diagnostik loggar** sidan):

![Bladet navigering till diagnostik loggar - arkivering mål](./media/stream-analytics-job-diagnostic-logs/image4.png)

Mer information om hur du konfigurerar diagnostik finns [samla in och använda diagnostikdata från resurserna i Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Diagnostik logga kategorier

För närvarande kan registrera vi två typer av loggar för diagnostik:

* **Redigera**. Registrerar loggar händelser som är relaterade till jobbet redigering operations: skapa, lägga till och ta bort inmatningar och matar ut, lägga till och uppdatera frågan, starta och stoppa jobbet jobb.
* **Körningen**. Samlar in händelser som inträffar under jobbkörningen:
    * Anslutningsfel
    * Databearbetning fel, inklusive:
        * Händelser som inte överensstämmer med frågedefinitionen (felaktig fälttyp och värden saknas fält och så vidare)
        * Fel för utvärdering av uttryck
    * Andra händelser och fel

## <a name="diagnostics-logs-schema"></a>Diagnostik loggar schema

Alla loggar lagras i JSON-format. Varje post innehåller följande vanliga strängfält:

Namn | Beskrivning
------- | -------
time | Tidsstämpeln (i UTC) för loggen.
resourceId | ID för den resurs som åtgärden utfördes, i versaler. Det innehåller prenumerations-ID, resursgruppen och Jobbnamnet. Till exempel   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. MYSTREAMINGJOB-STREAMANALYTICS/STREAMINGJOBS**.
category | Logga kategori, antingen **körning** eller **redigering**.
operationName | Namnet på åtgärden som är inloggad. Till exempel **skicka händelser: SQL-utdata skriva fel till mysqloutput**.
status | Status för åtgärden. Till exempel **misslyckades** eller **lyckades**.
nivå | Loggningsnivån. Till exempel **fel**, **varning**, eller **informations**.
properties | Logga post-specifik information serialiserad som en JSON-sträng. Mer information finns i följande avsnitt.

### <a name="execution-log-properties-schema"></a>Schemat för körning av loggen egenskaper

Körningsloggar ha information om händelser som inträffade under jobbkörningen Stream Analytics. Schemat för egenskaper varierar beroende på vilken typ av händelse. Vi har för närvarande följande typer av körningsloggar:

### <a name="data-errors"></a>Datafel

Alla fel som uppstår när jobbet bearbetar data är i den här kategorin loggar. Dessa loggar oftast skapas under data läses, serialisering och skrivåtgärder. Dessa loggar inkludera inte anslutningsfel. Anslutningsfel behandlas som allmänna händelser.

Namn | Beskrivning
------- | -------
Källa | Namnet på jobbet indata eller utdata där felet uppstod.
Meddelande | Meddelande som är associerade med fel.
Typ | Typen av fel. Till exempel **DataConversionError**, **CsvParserError**, eller **ServiceBusPropertyColumnMissingError**.
Data | Innehåller data som är användbar för att korrekt Leta reda på orsaken till felet. Trunkering, beroende på storleken som omfattas.

Beroende på den **operationName** värde, datafel har följande schema:
* **Serialisera händelser**. Serialisera händelser inträffar under händelsen läsåtgärder. De inträffar när data på indata inte uppfyller frågeschemat för något av följande skäl:
    * *Typmatchningsfel under händelsen (Tyskland) serialisera*: identifierar det fält som orsakar felet.
    * *Det går inte att läsa en händelse, ogiltig serialisering*: innehåller information om var indata där felet uppstod. Innehåller blobbnamnet på för blob-indata, förskjutning och en exempeldata.
* **Skicka händelser**. Skicka händelser sker under skrivåtgärder. De identifiera strömningshändelsen som orsakade felet.

### <a name="generic-events"></a>Allmänna händelser

Allmänna händelser omfatta allt annat.

Namn | Beskrivning
-------- | --------
Fel | (valfritt) Information om fel. Detta är vanligtvis undantagsinformation, om den är tillgänglig.
Meddelande| Loggmeddelande.
Typ | Typ av meddelande. Mappas till interna kategorisering av fel. Till exempel **JobValidationError** eller **BlobOutputAdapterInitializationFailure**.
Korrelations-ID | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) som unikt identifierar jobbkörningen. Alla loggposter för körning vid tiden för när jobbet startar tills jobbet slutar har samma **Korrelations-ID** värde.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språkreferens för Stream Analytics-fråga](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Strömma Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
