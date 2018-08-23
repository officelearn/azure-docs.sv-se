---
title: Testa Azure Stream Analytics-jobb med exempeldata
description: Hur du testar dina frågor i Stream Analytics-jobb.
keywords: Den här artikeln beskriver hur du använder Azure-portalen för att testa ett Azure Stream Analytics-jobb, Exempelindata och ladda upp exempeldata.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: d699d69362b2d28c205aab14a4bfb26570a68a4e
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42054780"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testa en Stream Analytics-fråga med exempeldata

Du kan överföra data och test exempelfrågor i Azure portal utan att starta eller stoppa ett jobb med hjälp av Azure Stream Analytics.

## <a name="upload-sample-data-and-test-the-query"></a>Ladda upp exempeldata och testa frågan

1. Logga in på Azure Portal. 

2. Leta upp ditt befintliga Stream Analytics-jobb och markera den.

3. På Stream Analytics-jobbet kan under den **Jobbtopologi** väljer **fråga** att öppna Query editor-fönstret. 

4. Testa din fråga med exempel på inmatade data genom att högerklicka på någon av dina indata.  Välj sedan **överför exempeldata från filen**. Data måste serialiseras i JSON, CSV eller AVRO.

    ![test för Stream analytics fråga frågeredigeraren](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. När överföringen är klar, väljer **testa** att testa den här frågan mot exempeldata som du har angett.

    ![Stream analytics-fråga exempeldata för Redigeraren för testning](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Om du behöver testa utdata för senare användning kan visas utdata från frågan i webbläsaren med en länk till nedladdningsresultaten. 

7. Upprepade gånger ändra din fråga och testa det igen för att se hur detta ändrar utdatan.

   ![Stream Analytics query editor exempel på utdata](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   När du använder flera utdata i en fråga visas resultatet på en separat flik och du kan enkelt växla mellan dem.

8. När du har kontrollerat de resultat som visas i webbläsaren, **spara** din fråga. Sedan **starta** jobbet, och låt den bearbeta inkommande händelser.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
