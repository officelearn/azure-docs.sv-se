---
title: Felsöka Azure Stream Analytics-frågor med hjälp av SELECT INTO
description: Den här artikeln beskriver hur du exempel på mellannivå datafrågor i Azure Stream Analytics-jobb med hjälp av SELECT INTO-instruktioner i frågesyntaxen.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: b056d4c29464451d3dc0ef62437f934535820489
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698000"
---
# <a name="debug-queries-by-using-select-into-statements"></a>Felsöka frågor med hjälp av SELECT INTO-instruktioner

I realtidsbearbetning av data, kan det vara användbart att att känna till hur data ser ut mitt i frågan. Eftersom indata eller stegen i Azure Stream Analytics-jobb kan läsa flera gånger, kan du skriva extra SELECT INTO-instruktioner. Då matar ut mellanliggande data till lagring och du kan inspektera för data, precis som *titta på variabler* göra när du felsöker ett program.

## <a name="use-select-into-to-check-the-data-stream"></a>Använda SELECT INTO för att kontrollera dataströmmen

Följande exempelfråga i Azure Stream Analytics-jobb har en strömindata, två inmatningar av referensdata och utdata till Azure Table Storage. Frågan kopplar ihop data från event hub och två referens BLOB-och hämta information om namn och kategori:

![SELECT INTO exempelfråga](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Observera att jobbet körs, men inga händelser genereras i utdata. På den **övervakning** panel som visas här kan du kan se att indata ger data, men du vet inte vilka steg i den **ansluta** orsakade alla händelser tas bort.

![Panelen övervakning](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
I så fall kan du lägga till några extra SELECT INTO-instruktioner för att ”logga” mellanliggande kopplingsresultatet och de data som läses från angivna indata.

I det här exemplet har vi lagt till två nya ”tillfälliga matar ut”. De kan vara valfri mottagare som du vill. Här använder vi Azure Storage som ett exempel:

![Att lägga till extra SELECT INTO-instruktioner](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Du kan sedan skriva om frågan så här:

![Ny SELECT INTO-frågan](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Nu starta jobbet igen och låt den körs under ett par minuter. Sedan fråga temp1 och temp2 med Visual Studio Cloud Explorer för att skapa följande tabeller:

**temp1 tabell**
![SELECT INTO temp1 tabell](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 tabell**
![SELECT INTO temp2 tabell](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Som du kan se både temp1 och temp2 har data och namnkolumnen fylls korrekt i temp2. Men eftersom det fortfarande finns inga data i utdata, är något fel:

![SELECT INTO output1 tabell utan data](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Av provtagning data, kan du vara nästan säker på att problemet är med andra KOPPLINGEN. Du kan hämta referensdata från blob och ta en titt:

![SELECT INTO ref-tabell](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Som du ser formatet för GUID i dessa referensdata skiljer sig från formatet för den [kolumnen i temp2 från]. Det är därför data inte kommer fram output1 som förväntat.

Du kan åtgärda dataformatet, ladda upp den för att referera till blob och försök igen:

![SELECT INTO temporär tabell](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Den här tiden kan data i utdata formateras och fylls i som förväntat.

![SELECT INTO slutliga tabell](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Få hjälp

För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

