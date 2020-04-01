---
title: Felsöka Azure Stream Analytics-frågor
description: I den här artikeln beskrivs tekniker för att felsöka dina frågor i Azure Stream Analytics-jobb.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: f049dc6d1261a8201cf79d1779e522b30d13c4b0
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409445"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Felsöka Azure Stream Analytics-frågor

I den här artikeln beskrivs vanliga problem med att utveckla Stream Analytics-frågor och hur du felsöker dem.

I den här artikeln beskrivs vanliga problem med att utveckla Azure Stream Analytics-frågor, hur du felsöker frågeproblem och hur du åtgärdar problemen. Många felsökningssteg kräver att diagnostikloggar aktiveras för ditt Stream Analytics-jobb. Om du inte har aktiverat diagnostikloggar läser [du Felsöka Azure Stream Analytics med hjälp av diagnostikloggar](stream-analytics-job-diagnostic-logs.md).

## <a name="query-is-not-producing-expected-output"></a>Frågan producerar inte förväntad utdata

1.  Undersök fel genom att testa lokalt:

    - På Azure-portalen väljer du **Testa**på fliken **Fråga** . Använd de hämtade exempeldata för att [testa frågan](stream-analytics-test-query.md). Undersök eventuella fel och försök att korrigera dem.   
    - Du kan också [testa din fråga lokalt](stream-analytics-live-data-local-testing.md) med Hjälp av Azure Stream Analytics-verktyg för Visual Studio eller Visual Studio [Code](visual-studio-code-local-run-live-input.md). 

2.  [Felsöka frågor steg för steg lokalt med hjälp av jobbdiagram](debug-locally-using-job-diagram.md) i Azure Stream Analytics-verktyg för Visual Studio. Jobbdiagrammet visar hur data flödar från indatakällor (Event Hub, IoT Hub, etc.) genom flera frågesteg och slutligen till utdatamottagare. Varje frågesteg mappas till en tillfällig resultatuppsättning som definieras i skriptet med hjälp av WITH-satsen. Du kan visa data, liksom mått, i varje mellanliggande resultatuppsättning för att hitta källan till problemet.

    ![Förhandsgranskningsresultat för jobbdiagram](./media/debug-locally-using-job-diagram/preview-result.png)

3.  Om du använder [**Timestamp By**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)kontrollerar du att händelserna har tidsstämplar som är större än [jobbets starttid](stream-analytics-out-of-order-and-late-events.md).

4.  Eliminera vanliga fallgropar, till exempel:
    - En [**WHERE-sats**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) i frågan filtrerade bort alla händelser, vilket förhindrar att utdata genereras.
    - En [**CAST-funktion**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) misslyckas, vilket gör att jobbet misslyckas. Använd TRY_CAST i stället [**för**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) att undvika skrivfel.
    - När du använder fönsterfunktioner väntar du tills hela fönstrets varaktighet visas för att se ett utdata från frågan.
    - Tidsstämpeln för händelser föregår jobbets starttid och händelser tas bort.
    - [**JOIN-villkor**](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) matchar inte. Om det inte finns några matchningar blir det nollutdata.

5.  Se till att händelseordningsprinciper är konfigurerade som förväntat. Gå till **Inställningar** och välj [**Händelsebeställning**](stream-analytics-out-of-order-and-late-events.md). Principen tillämpas *inte* när du använder knappen **Testa** för att testa frågan. Detta resultat är en skillnad mellan att testa i webbläsaren jämfört med att köra jobbet i produktion. 

6. Felsöka med hjälp av gransknings- och diagnostikloggar:
    - Använd [granskningsloggar](../azure-resource-manager/resource-group-audit.md)och filtrera för att identifiera och felsöka fel.
    - Använd [jobbdiagnostiska loggar](stream-analytics-job-diagnostic-logs.md) för att identifiera och felsöka fel.

## <a name="resource-utilization-is-high"></a>Resursutnyttjandet är högt

Se till att du drar nytta av parallellisering i Azure Stream Analytics. Du kan lära dig att [skala med frågeparallalisering](stream-analytics-parallelization.md) av Stream Analytics-jobb genom att konfigurera indatapartitioner och justera analysfrågedefinitionen.

## <a name="debug-queries-progressively"></a>Felsöka frågor progressivt

I realtid databehandling, att veta hur data ser ut i mitten av frågan kan vara till hjälp. Detta kan du se med hjälp av jobbdiagrammet i Visual Studio. Om du inte har Visual Studio kan du vidta ytterligare åtgärder för att mata ut mellanliggande data.

Eftersom indata eller steg i ett Azure Stream Analytics-jobb kan läsas flera gånger kan du skriva extra SELECT INTO-satser. Om du gör det matas mellanliggande data ut i lagring och du kan kontrollera att data är korrekta, precis som *klockvariabler* gör när du felsöker ett program.

Följande exempelfråga i ett Azure Stream Analytics-jobb har en indata, två referensdataindata och en utdata till Azure Table Storage. Frågan sammanfogar data från händelsehubben och två referensblobar för att hämta namn- och kategoriinformation:

![Exempel på Stream Analytics VÄLJ TILL-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Observera att jobbet körs, men inga händelser produceras i utdata. På **panelen Övervakning,** som visas här, kan du se att indata producerar data, men du vet inte vilket steg i **JOIN** som orsakade att alla händelser togs bort.

![Övervakningspanelen för Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

I det här fallet kan du lägga till några extra SELECT INTO-satser för att "logga" mellanliggande JOIN-resultat och data som läss från indata.

I det här exemplet har vi lagt till två nya "tillfälliga utdata". De kan vara vilken diskbänk du vill. Här använder vi Azure Storage som exempel:

![Lägga till extra SELECT INTO-satser i Stream Analytics-frågan](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Du kan sedan skriva om frågan så här:

![Omskriven VÄLJ I Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Nu börjar jobbet igen, och låt det köras i några minuter. Fråga sedan temp1 och temp2 med Visual Studio Cloud Explorer för att skapa följande tabeller:

**temp1-tabell**
![VÄLJ TEMPORÄR TABELLStrömanalysfråga](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2-tabell**
![VÄLJ TEMPORÄRD2-tabell Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Som du kan se har temp1 och temp2 båda data och namnkolumnen fylls i korrekt i temp2. Men eftersom det fortfarande inte finns några data i produktionen, är något fel:

![VÄLJ I tabellen utdata1 utan dataStream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Genom att prova data kan du vara nästan säker på att problemet är med den andra JOIN. Du kan hämta referensdata från blobben och ta en titt:

![VÄLJ I referenstabell Stream Analytics-fråga](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Som du kan se skiljer sig formatet för GUID i dessa referensdata från formatet för kolumnen [från] i temp2. Det är därför uppgifterna inte kom fram i output1 som förväntat.

Du kan åtgärda dataformatet, ladda upp det till referensblobb och försöka igen:

![VÄLJ I frågan om streamanalys för temp-tabell](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Den här gången formateras och fylls data i utdata som förväntat.

![VÄLJ I frågan om streamanalys i finaltabellen](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Få hjälp

Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
