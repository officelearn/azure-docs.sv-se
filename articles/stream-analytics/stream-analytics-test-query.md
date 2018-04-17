---
title: Testa Azure Stream Analytics-jobbet med exempeldata | Microsoft Docs
description: Hur du testar dina frågor i Stream Analytics-jobb.
keywords: testa ett jobb, ange provtagning, överför exempel datum
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>Testa din Stream Analytics-fråga med exempeldata

Du kan överföra data och testa exempelfrågor i portalen utan att starta eller stoppa ett jobb med hjälp av Azure Stream Analytics.

## <a name="upload-sample-data-and-test-the-query"></a>Ladda upp exempeldata och testa frågan

1. Navigera till en befintlig Stream Analytics-jobbet > klickar du på **frågan** att öppna frågeredigerarens fönster. 

2. Högerklicka på någon av dina inmatningar för att testa frågan med exempeldata i indata, och välj sedan **ladda upp exempeldata från filen**. För närvarande kan du överföra endast JSON-formaterade data. Om dina data är i ett annat format, till exempel CSV, bör du konvertera den till JSON innan du laddar upp. Du kan använda alla opensource konverteringsverktyget som [CSV-fil för JSON-konverteraren](http://www.convertcsv.com/csv-to-json.htm) konvertera data till JSON.

    ![Stream analytics query editor Testa fråga](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. När överföringen är klar klickar du på **testa** att testa den här frågan mot exempeldata som du har angett.

    ![Stream analytics fråga editor test exempeldata](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. Om du vill spara testet utdata för senare användning visas utdata från frågan i webbläsaren med en länk till nedladdningsresultaten. Du kan nu enkelt och upprepade gånger ändra din fråga och testa det flera gånger för att se hur utdata ändras.

   ![Stream Analytics query editor exempel på utdata](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Du kan se resultaten för varje utdata separat och växla mellan dem när du använder flera utdata i en fråga. När du har kontrollerat resultatet ska visas i webbläsaren, kan du spara din fråga starta jobbet och kan bearbeta händelser utan fel.
För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
