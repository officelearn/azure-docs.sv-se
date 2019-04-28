---
title: Felsöka Azure Stream Analytics-frågor
description: Den här artikeln beskrivs metoder för att felsöka dina frågor i Azure Stream Analytics-jobb.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7a1e440a8dc8f518e272df9e126771df54390ed5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60762488"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Felsöka Azure Stream Analytics-frågor

Den här artikeln beskrivs vanliga problem med att utveckla Stream Analytics-frågor och hur du felsöker dem.

## <a name="query-is-not-producing-expected-output"></a>Fråga producerar utdata som förväntas 
1.  Undersök fel genom att testa lokalt:
    - På den **fråga** fliken **Test**. Använda nedladdade exempeldata för att [testa frågan](stream-analytics-test-query.md). Granska eventuella fel och försöka åtgärda dem.   
    - Du kan också [testa frågan direkt på live indata](stream-analytics-live-data-local-testing.md) med Stream Analytics-verktyg för Visual Studio.

2.  Om du använder [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), kontrollera att händelserna har tidsstämplar som är större än den [jobbet starttid](stream-analytics-out-of-order-and-late-events.md).

3.  Eliminera vanliga fallgropar, till exempel:
    - En [ **där** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) sats i frågan filtrerade bort alla händelser som förhindrar att några utdata som genereras.
    - En [ **CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) misslyckas, vilket gör jobbet misslyckas. För att undvika fel av typen cast kan använda [ **TRY_CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) i stället.
    - När du använder fönsterfunktioner, vänta tills hela fönstret-tid för att se utdata från frågan.
    - Tidsstämpel för händelser som kommer före jobbets starttid och därför att händelser utelämnas.

4.  Se till att händelsen skrivordning principerna är konfigurerade som förväntat. Gå till den **inställningar** och välj [ **Händelseordning**](stream-analytics-out-of-order-and-late-events.md). Principen är *inte* tillämpas när du använder den **testa** knappen för att testa frågan. Resultatet är en skillnad mellan att testa i webbläsaren och att köra jobbet i produktion. 

5. Felsöka med hjälp av gransknings- och diagnostikloggar:
    - Använd [granskningsloggar](../azure-resource-manager/resource-group-audit.md), och filter för att identifiera och felsöka fel.
    - Använd [jobbet diagnostikloggar](stream-analytics-job-diagnostic-logs.md) att identifiera och felsöka fel.

## <a name="job-is-consuming-too-many-streaming-units"></a>Jobb förbrukar för många enheter för strömning
Se till att du dra nytta av parallellisering i Azure Stream Analytics. Du kan lära dig att [skala med frågeparallellisering](stream-analytics-parallelization.md) frågedefinition i analyser av Stream Analytics-jobb genom att konfigurera inkommande partitioner och justering.

## <a name="debug-queries-progressively"></a>Felsöka frågor progressivt

I realtidsbearbetning av data, kan det vara användbart att att känna till hur data ser ut mitt i frågan. Eftersom indata eller stegen i Azure Stream Analytics-jobb kan läsa flera gånger, kan du skriva extra SELECT INTO-instruktioner. Då matar ut mellanliggande data till lagring och du kan inspektera för data, precis som *titta på variabler* göra när du felsöker ett program.

Följande exempelfråga i Azure Stream Analytics-jobb har en strömindata, två inmatningar av referensdata och utdata till Azure Table Storage. Frågan kopplar ihop data från event hub och två referens BLOB-och hämta information om namn och kategori:

![Stream Analytics SELECT INTO exempelfråga](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Observera att jobbet körs, men inga händelser genereras i utdata. På den **övervakning** panel som visas här kan du kan se att indata ger data, men du vet inte vilka steg i den **ansluta** orsakade alla händelser tas bort.

![Övervakning av Stream Analytics-panel](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
I så fall kan du lägga till några extra SELECT INTO-instruktioner för att ”logga” mellanliggande kopplingsresultatet och de data som läses från angivna indata.

I det här exemplet har vi lagt till två nya ”tillfälliga matar ut”. De kan vara valfri mottagare som du vill. Här använder vi Azure Storage som ett exempel:

![Att lägga till extra SELECT INTO-instruktioner i Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Du kan sedan skriva om frågan så här:

![Ny väljer i Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Nu starta jobbet igen och låt den körs under ett par minuter. Sedan fråga temp1 och temp2 med Visual Studio Cloud Explorer för att skapa följande tabeller:

**temp1 tabell**
![SELECT INTO temp1 tabell Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 tabell**
![SELECT INTO temp2 tabell Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Som du kan se både temp1 och temp2 har data och namnkolumnen fylls korrekt i temp2. Men eftersom det fortfarande finns inga data i utdata, är något fel:

![SELECT INTO output1 tabell med inga data Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Av provtagning data, kan du vara nästan säker på att problemet är med andra KOPPLINGEN. Du kan hämta referensdata från blob och ta en titt:

![SELECT INTO ref tabell Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Som du ser formatet för GUID i dessa referensdata skiljer sig från formatet för den [kolumnen i temp2 från]. Det är därför data inte kommer fram output1 som förväntat.

Du kan åtgärda dataformatet, ladda upp den för att referera till blob och försök igen:

![SELECT INTO temporära tabellen Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Den här tiden kan data i utdata formateras och fylls i som förväntat.

![SELECT INTO slutliga tabellen Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Få hjälp

För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)