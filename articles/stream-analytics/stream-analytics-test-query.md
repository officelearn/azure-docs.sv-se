---
title: Testa Azure Stream Analytics-jobb med exempeldata
description: Den här artikeln beskriver hur du använder Azure-portalen för att testa ett Azure Stream Analytics-jobb, Exempelindata och ladda upp exempeldata.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: 1a9caf83c6f4cd4ed15290afc872043c11234552
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508714"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testa en Stream Analytics-fråga med exempeldata

Med hjälp av Azure Stream Analytics kan du exempeldata från indata eller ladda upp exempeldata för att testa frågor i Azure portal utan att starta eller stoppa ett jobb.

## <a name="upload-or-sample-data-from-a-live-source-to-test-the-query"></a>Ladda upp eller exempeldata från en källa som live för att testa frågan

1. Logga in på Azure Portal. 

2. Leta upp ditt befintliga Stream Analytics-jobb och markera den.

3. På Stream Analytics-jobbet kan under den **Jobbtopologi** väljer **fråga** att öppna Query editor-fönstret. 

4. Om du vill testa frågan kan du sedan antingen exempeldata från en live indata eller ladda upp från en fil. Data måste serialiseras i JSON, CSV eller AVRO. Exempelindata måste vara kodad i UTF-8 och inte komprimeras. Endast kommatecken (,) avgränsare stöds för att testa CSV indata på portalen.

    1. Med live indata: Högerklicka på någon av dina indata. Välj sedan **exempeldata från indata**. Du kan ange varaktigheten för exemplet på nästa skärm. Sampling händelser från en livekällan kommer att hämta upp till 1 000 händelser eller 1 MB (beroende på vilket som inträffar först), så data samplas inte kanske motsvarar det heltid intervall som anges.

    1. Med hjälp av filen: Högerklicka på någon av dina indata. Välj sedan **överför exempeldata från filen**. 

    ![test för Stream analytics fråga frågeredigeraren](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. När datainsamling och ladda upp är avslutad, Välj **testa** att testa den här frågan mot exempeldata som du har angett.

    ![Stream analytics-fråga exempeldata för Redigeraren för testning](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Om du behöver testa utdata för senare användning kan visas utdata från frågan i webbläsaren med en länk till nedladdningsresultaten. 

7. Upprepade gånger ändra din fråga och testa det igen för att se hur detta ändrar utdatan.

   ![Stream Analytics query editor exempel på utdata](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   När du använder flera utdata i en fråga visas resultatet på en separat flik och du kan enkelt växla mellan dem.

8. När du har kontrollerat de resultat som visas i webbläsaren, **spara** din fråga. Sedan **starta** jobbet, och låt den bearbeta inkommande händelser.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
