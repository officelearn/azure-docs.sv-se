---
title: Testa ett Azure Stream Analytics-jobb med exempeldata
description: I den här artikeln beskrivs hur du använder Azure-portalen för att testa ett Azure Stream Analytics-jobb, exempeldata och ladda upp exempeldata.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: de2b99666dbed4eaec3db3e56800e9a7352e10e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898387"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Testa ett Azure Stream Analytics-jobb i portalen

I Azure Stream Analytics kan du testa din fråga utan att starta eller stoppa jobbet. Du kan testa frågor om inkommande data från dina strömmande källor eller ladda upp exempeldata från en lokal fil på Azure Portal. Du kan också testa frågor lokalt från lokala exempeldata eller livedata i [Visual Studio](stream-analytics-live-data-local-testing.md) och [Visual Studio Code](visual-studio-code-local-run-live-input.md).

## <a name="automatically-sample-incoming-data-from-input"></a>Ta automatiskt prover på inkommande data från indata

Azure Stream Analytics hämtar automatiskt händelser från dina direktuppspelningsindata. Du kan köra frågor på standardexemplet eller ange en viss tidsram för exemplet.

1. Logga in på Azure Portal.

2. Leta reda på och välj ditt befintliga Stream Analytics-jobb.

3. Välj **Fråga** under rubriken **Jobbtopologi under jobbtopologin** på jobbsidan Stream Analytics för att öppna fönstret Frågeredigeraren. 

4. Om du vill visa en exempellista över inkommande händelser markerar du indata med filikonen så visas exempelhändelserna automatiskt i **förhandsgranskningen av indata**.

   a. Serialiseringstypen för dina data identifieras automatiskt om dess JSON eller CSV. Du kan manuellt ändra det också till JSON, CSV, AVRO genom att ändra alternativet i rullgardinsmenyn.
    
   b. Använd väljaren för att visa dina data i **tabell-** eller **råformat.**
    
   c. Om dina data som visas inte är aktuella väljer du **Uppdatera** för att se de senaste händelserna.

   Följande tabell är ett exempel på data i **tabellformat:**

   ![Exempelindata från Azure Stream Analytics i tabellformat](./media/stream-analytics-test-query/asa-sample-table.png)

   Följande tabell är ett exempel på data i **raw-format:**

   ![Exempelindata från Azure Stream Analytics i råformat](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Om du vill testa frågan med inkommande data väljer du **Testa fråga**. Resultaten visas på fliken **Testresultat.** Du kan också välja **Hämta resultat** för att hämta resultaten.

   ![Exempel på testfrågeresultat för Azure Stream Analytics](./media/stream-analytics-test-query/asa-test-query.png)

6. Om du vill testa frågan mot ett visst tidsintervall för inkommande händelser väljer du **Välj tidsintervall**.
   
   ![Tidsintervallet för Azure Stream Analytics för inkommande exempelhändelser](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Ange tidsintervallet för de händelser som du vill använda för att testa frågan och välj **Exempel**. Inom den tidsramen kan du hämta upp till 1 000 händelser eller 1 MB, beroende på vilket som inträffar först.

   ![Azure Stream Analytics anger tidsintervall för inkommande exempelhändelser](./media/stream-analytics-test-query/asa-set-time-range.png)

8. När du har samplat händelser för valt tidsintervall visas de på fliken **Förhandsgranskning av indata.**

   ![Testresultat för Azure Stream Analytics-vyn](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Välj **Återställ** om du vill visa exempellistan över inkommande händelser. Om du väljer **Återställ**går valet av tidsintervall bort. Välj **Testa fråga** om du vill testa frågan och granska resultaten på fliken **Testresultat.**

10. När du gör ändringar i frågan väljer du **Spara fråga** för att testa den nya frågelogiken. På så sätt kan du iterativt ändra frågan och testa den igen för att se hur utdata ändras.

11. När du har verifierat resultaten som visas i webbläsaren är du redo att **starta** jobbet.

## <a name="upload-sample-data-from-a-local-file"></a>Ladda upp exempeldata från en lokal fil

I stället för att använda realtidsdata kan du använda exempeldata från en lokal fil för att testa din Azure Stream Analytics-fråga.

1. Logga in på Azure Portal.
   
2. Leta reda på ditt befintliga Stream Analytics-jobb och välj det.

3. Välj **Fråga** under rubriken **Jobbtopologi under jobbtopologin** på jobbsidan Stream Analytics för att öppna fönstret Frågeredigeraren.

4. Om du vill testa frågan med en lokal fil väljer du **Ladda upp exempelindata** på fliken **Förhandsgranskning av indata.** 

   ![Exempelfilen för azure stream analytics-överföring](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Ladda upp den lokala filen för att testa frågan. Du kan bara ladda upp filer med JSON-, CSV- eller AVRO-formaten. Välj **OK**.

   ![Exempelfilen för azure stream analytics-överföring](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Så fort du laddar upp filen kan du också se filinnehållet i formuläret som en tabell eller i det råformat. Om du väljer **Återställ**återgår exempeldata till de inkommande indata som förklaras i föregående avsnitt. Du kan ladda upp alla andra filer för att testa frågan när som helst.

7. Välj **Testfråga** om du vill testa frågan mot den uppladdade exempelfilen.

8. Testresultat visas baserat på din fråga. Du kan ändra frågan och välja **Spara fråga** för att testa den nya frågelogiken. På så sätt kan du iterativt ändra frågan och testa den igen för att se hur utdata ändras.

9. När du använder flera utdata i frågan visas resultaten baserat på valda utdata. 

   ![Azure Stream Analytics vald utdata](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. När du har verifierat resultaten som visas i webbläsaren kan du **starta** jobbet.

## <a name="next-steps"></a>Nästa steg
* [Bygg en IoT-lösning med hjälp av Stream Analytics:](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)den här guiden hjälper dig att bygga en end-to-end-lösning med en datagenerator som simulerar trafik vid en vägtullmonter.

* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Frågeexempel för vanliga Användningsmönster i Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Förstå indata för Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md)
