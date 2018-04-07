---
title: Felsöka Azure Stream Analytics-frågor med hjälp av SELECT INTO
description: Den här artikeln beskriver hur du exempel halva datafrågor Azure Stream Analytics-jobb med hjälp av SELECT INTO-instruktioner i frågesyntaxen.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: ccaa6203e4bfe52758e26416646f9152ac5378ea
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="debug-queries-by-using-select-into-statements"></a>Felsöka frågor med hjälp av SELECT INTO-instruktioner

I realtidsbearbetning av data, kan det vara bra att känna till hur data ser ut mitt i frågan. Eftersom indata- och stegen för ett Azure Stream Analytics-jobb kan läsa flera gånger, kan du skriva extra SELECT INTO-instruktioner. Gör detta matar ut mellanliggande data till lagring och gör att du kan granska är korrekt av data, precis som *titta på variabler* göra när du felsöker ett program.

## <a name="use-select-into-to-check-the-data-stream"></a>Använd SELECT INTO för att kontrollera dataströmmen

Följande exempelfråga i Azure Stream Analytics-jobbet har en strömindata, två referens indata och utdata till Azure Table Storage. Frågan kopplar ihop data från händelsehubb och två referens BLOB för att hämta information om namn och kategori:

![SELECT INTO exempelfråga](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Observera att jobbet körs, men inga händelser genereras i utdata. På den **övervakning** ikonen som visas här, kan du se att indata är producera data, men du inte vet vilka steg i den **ansluta** orsakade alla händelser tas bort.

![Panelen övervakning](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
I så fall kan du lägga till några extra SELECT INTO-instruktioner för att ”logga” mellanliggande kopplingsresultatet och de data som läses från angivna indata.

I det här exemplet har vi lagt till två nya ”tillfällig utdata”. De kan vara alla mottagare som du vill. Här kan vi använda Azure Storage som exempel:

![Lägga till extra SELECT INTO-instruktioner](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Du kan sedan skriva om frågan så här:

![Omskrivet SELECT INTO-frågan](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Nu startar jobbet igen och låt den kör några minuter. Fråga sedan temp1 och temp2 med Visual Studio Cloud Explorer för att skapa följande tabeller:

**temp1 tabell**
![SELECT INTO temp1 tabell](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 tabell**
![SELECT INTO temp2 tabell](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Som du kan se temp1 och temp2 har data och namnkolumnen fylls korrekt i temp2. Men eftersom det fortfarande finns inga data i utdata, det är något fel:

![SELECT INTO output1 tabell utan data](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Genom att ta prov data vara du nästan säkert att problemet beror på den andra KOPPLINGEN. Du kan hämta referensdata från blob och titta:

![SELECT INTO ref-tabell](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Som du ser formatet för GUID i den här referensdata skiljer sig från formatet på den [kolumn i temp2 från]. Det är därför data inte kommer fram output1 som förväntat.

Du kan åtgärda dataformatet, ladda upp den för att referera till blob och försök igen:

![SELECT INTO temporära tabellen](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Den här tiden kan data i utdata formateras och fyllts som förväntat.

![SELECT INTO slutliga tabell](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Få hjälp

För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

