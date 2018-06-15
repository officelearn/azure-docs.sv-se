---
title: Testa ett Azure Stream Analytics-jobb med exempeldata
description: Hur du testar dina frågor i Stream Analytics-jobb.
keywords: Den här artikeln beskriver hur du använder Azure-portalen för att testa en Azure Stream Analytics-jobb, Exempelindata och ladda upp exempeldata.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 3dc9091934f3db8ededc13f74d2f302eccace4d6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312998"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testa en Stream Analytics-fråga med exempeldata

Du kan överföra data och testa exempelfrågor i Azure portal utan att starta eller stoppa ett jobb med hjälp av Azure Stream Analytics.

## <a name="upload-sample-data-and-test-the-query"></a>Ladda upp exempeldata och testa frågan

1. Logga in på Azure Portal. 

2. Leta upp din befintliga Stream Analytics-jobbet och välja den.

3. På Stream Analytics-jobbet sidan under den **jobbet topologi** rubrik, Välj **frågan** att öppna frågeredigerarens fönster. 

4. Högerklicka på någon av dina inmatningar för att testa frågan med indata exempeldata.  Välj sedan **ladda upp exempeldata från filen**.

   Data måste vara endast JSON-formaterade data. Om dina data är i ett annat format, till exempel CSV, bör du konvertera den till JSON innan du laddar upp. Du kan använda alla opensource konverteringsverktyget som [CSV-fil för JSON-konverteraren](http://www.convertcsv.com/csv-to-json.htm) konvertera data till JSON.

    ![Stream analytics query editor Testa fråga](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. När överföringen är klar väljer du **testa** att testa den här frågan mot exempeldata som du har angett.

    ![Stream analytics fråga editor test exempeldata](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Om du behöver testa utdata för senare användning visas utdata från frågan i webbläsaren med en länk till nedladdningsresultaten. 

7. Upprepade gånger ändra din fråga och testa den igen för att se hur utdata ändras.

   ![Stream Analytics query editor exempel på utdata](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   När du använder flera utdata i en fråga visas resultaten på separata flikar och du kan enkelt växla mellan dem.

8. När du har kontrollerat resultatet ska visas i webbläsaren, **spara** frågan. Sedan **starta** jobbet, och låt den bearbeta inkommande händelser.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
