---
title: Felsöka Azure Stream Analytics frågor
description: Den här artikeln beskriver tekniker för att felsöka dina frågor i Azure Stream Analytics-jobb.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: bf0740bbdd4754aeba43e64f1076a1bea33cffc6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844435"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Felsöka Azure Stream Analytics frågor

I den här artikeln beskrivs vanliga problem med att utveckla Stream Analytics frågor och hur du felsöker dem.

## <a name="query-is-not-producing-expected-output"></a>Frågan producerar inte förväntade utdata
1.  Undersök fel genom att testa lokalt:
    - På Azure Portal väljer du **test**på fliken **fråga** . Använd de hämtade exempel data för att [testa frågan](stream-analytics-test-query.md). Undersök eventuella fel och försök att åtgärda dem.   
    - Du kan också [testa din fråga lokalt](stream-analytics-live-data-local-testing.md) med Azure Stream Analytics verktyg för Visual Studio eller [Visual Studio Code](visual-studio-code-local-run-live-input.md). 

2.  [Felsök frågor steg för steg lokalt med hjälp av jobb diagram](debug-locally-using-job-diagram.md) i Azure Stream Analytics verktyg för Visual Studio. Jobb diagrammet är att visa hur data flödar från inmatnings källor (Händelsehubben, IoT Hub osv.) genom flera fråge steg och slutligen utdata till sinks. Varje fråge steg mappas till en tillfällig resultat uppsättning som definierats i skriptet med instruktionen WITH. Du kan visa informationen och måtten i varje fråge steg i varje steg i mellanliggande resultat uppsättning för att hitta källan till problemet.
    resultat](./media/debug-locally-using-job-diagram/preview-result.png) för förhands granskning av ![jobb diagram

3.  Om du använder [**timestamp by**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)kontrollerar du att händelserna har tidsstämplar som är större än [jobbets start tid](stream-analytics-out-of-order-and-late-events.md).

4.  Eliminera vanliga fall GRO par, till exempel:
    - En [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) -sats i frågan filtrerade ut alla händelser och förhindrar att utdata genereras.
    - En [**Cast**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) -funktion Miss lyckas, vilket medför att jobbet Miss lyckas. Undvik typ konverterings felen genom att använda [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) i stället.
    - När du använder fönster funktioner väntar du tills hela fönstrets varaktighet visar utdata från frågan.
    - Tidsstämpeln för händelser föregår jobbets start tid och därmed ignoreras händelser.

5.  Se till att principer för händelse ordning konfigureras som förväntat. Gå till **Inställningar** och välj [**händelse ordning**](stream-analytics-out-of-order-and-late-events.md). Principen tillämpas *inte* när du använder **test** knappen för att testa frågan. Resultatet är en skillnad mellan testning i webbläsaren jämfört med att köra jobbet i produktion. 

6. Felsöka med hjälp av gransknings-och diagnostikloggar:
    - Använd [gransknings loggar](../azure-resource-manager/resource-group-audit.md)och filtrera för att identifiera och felsöka fel.
    - Använd [jobb diagnostiska loggar](stream-analytics-job-diagnostic-logs.md) för att identifiera och felsöka fel.

## <a name="job-is-consuming-too-many-streaming-units"></a>Jobbet tar för många enheter för strömning
Se till att du utnyttjar parallellisering i Azure Stream Analytics. Du kan lära dig att [skala med parallellisering](stream-analytics-parallelization.md) av Stream Analytics jobb genom att konfigurera inpartitioner och justera analys frågans definition.

## <a name="debug-queries-progressively"></a>Felsöka frågor progressivt

Vid data bearbetning i real tid vet du hur data ser ut som i mitten av frågan kan vara till hjälp. Eftersom indata eller steg i ett Azure Stream Analytics jobb kan läsas flera gånger, kan du skriva extra SELECT INTO-uttryck. Genom att göra detta visas mellanliggande data i lagrings utrymmet och du kan kontrol lera att data är korrekta, precis som *Se variabler* gör när du felsöker ett program.

Följande exempel fråga i ett Azure Stream Analytics jobb har en data ström, två referens data inmatningar och utdata till Azure Table Storage. Frågan kopplar data från händelsehubben och två referens blobbar för att hämta namn och kategori information:

![Exempel Stream Analytics välja i fråga](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Observera att jobbet körs, men inga händelser skapas i utdata. På panelen **övervakning** , som visas här, kan du se att indata skapar data, men du vet inte vilket steg i **kopplingen** som orsakade alla händelser som skulle tas bort.

![Panelen Stream Analytics övervakning](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

I så fall kan du lägga till några extra SELECT INTO-uttryck för att "Logga" de mellanliggande KOPPLINGs resultaten och de data som läses från indata.

I det här exemplet har vi lagt till två nya "tillfälliga utdata". De kan vara vilken mottagare som helst. Här använder vi Azure Storage som exempel:

![Lägga till extra SELECT INTO-uttryck i Stream Analytics fråga](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Du kan sedan skriva om frågan så här:

![Skrev om SELECT INTO Stream Analytics fråga](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Starta nu jobbet igen och låt det köras i några minuter. Fråga sedan temp1 och temp2 med Visual Studio Cloud Explorer för att skapa följande tabeller:

**temp1 tabell**
![Välj i temp1 Table Stream Analytics fråga](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 tabell**
![Välj i temp2 Table Stream Analytics fråga](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Som du kan se har temp1 och temp2 båda data och kolumnen namn är korrekt ifylld i temp2. Men eftersom det fortfarande inte finns några data i utdata är något fel:

![Välj i output1-tabellen utan data Stream Analytics fråga](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Genom att sampla data kan du vara nästan säker på att problemet är med den andra anslutningen. Du kan hämta referens data från blobben och ta en titt:

![Välj i ref-tabell Stream Analytics fråga](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Som du ser skiljer sig formatet på GUID i denna referens data från formatet i kolumnen [från] i temp2. Det är därför som data inte anlände i output1 som förväntat.

Du kan åtgärda data formatet, överföra det till referens-blob och försöka igen:

![Välj i temporär tabell Stream Analytics fråga](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Den här gången är data i utdata formaterade och ifyllda som förväntat.

![Välj i den sista tabellen Stream Analytics fråga](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Få hjälp

För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
