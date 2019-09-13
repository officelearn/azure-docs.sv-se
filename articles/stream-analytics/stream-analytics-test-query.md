---
title: Testa ett Azure Stream Analytics jobb med exempel data
description: I den här artikeln beskrivs hur du använder Azure Portal för att testa ett Azure Stream Analytics jobb, exempel på indata och ladda upp exempel data.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: 9c67d511f6c94c8b9af034835e149875304e2235
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918972"
---
# <a name="test-an-azure-stream-analytics-job-with-sample-data"></a>Testa ett Azure Stream Analytics jobb med exempel data

I Azure Stream Analytics kan du testa din fråga utan att starta eller stoppa jobbet. Du kan testa frågor på inkommande data från din inkommande mottagare eller ladda upp exempel data från en lokal fil på Azure Portal. Du kan också testa frågor lokalt från dina lokala exempel data eller real tids data i [Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-live-data-local-testing) och [Visual Studio Code](https://docs.microsoft.com/en-us/azure/stream-analytics/vscode-local-run). 

## <a name="sample-incoming-data-from-input"></a>Exempel på inkommande data från indata

Azure Stream Analytics automatiskt hämtar händelser från dina strömmande data. Du kan köra frågor på standardprovet eller ange en bestämd tidsram för exemplet.

1. Logga in på Azure Portal.

2. Leta upp och välj ditt befintliga Stream Analytics-jobb.

3. På sidan Stream Analytics jobb går du till rubriken **jobb Topology** och väljer **fråga** för att öppna fönstret Frågeredigeraren. 

4. Om du vill se en exempel lista över inkommande händelser väljer du ikonen indata med fil och exempel händelser visas automatiskt i **förhands granskningen**. 

   a. Serialiserings typen för dina data identifieras automatiskt om dess JSON eller CSV. Du kan manuellt ändra det och till JSON, CSV, AVRO genom att ändra alternativet i list menyn.
    
   b. Använd väljaren för att visa dina data i **tabell** -eller **RAW** -format.
    
   c. Om data som visas inte är aktuella väljer du **Uppdatera** för att se de senaste händelserna.

   Följande tabell är ett exempel på data i **tabell format**:

   ![Azure Stream Analytics exempel på indatatyp i tabell format](./media/stream-analytics-test-query/asa-sample-table.png)

   Följande tabell är ett exempel på data i RAW- **format**:

   ![Azure Stream Analytics exempel på indata i RAW-format](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Om du vill testa din fråga med inkommande data väljer du **test fråga**. Resultaten visas på fliken **test resultat** . Du kan också välja **Hämta resultat** för att hämta resultatet.

   ![Exempel på test frågeresultaten för Azure Stream Analytics](./media/stream-analytics-test-query/asa-test-query.png)

6. Om du vill testa din fråga mot ett speciellt tidsintervall för inkommande händelser väljer du **Välj tidsintervall**.
   
   ![Azure Stream Analytics tidsintervall för inkommande exempel händelser](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Ange tidsintervallet för de händelser som du vill använda för att testa frågan och välj **exempel**. Inom denna tidsram kan du hämta upp till 1000 händelser eller 1 MB, beroende på vilket som kommer först.

   ![Azure Stream Analytics ange tidsintervall för inkommande exempel händelser](./media/stream-analytics-test-query/asa-set-time-range.png)

8. När händelserna samplas för det valda tidsintervallet visas de på fliken **förhands granskning av indatakälla** .

   ![Azure Stream Analytics Visa test resultat](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Välj **Återställ** om du vill se exempel listan för inkommande händelser. Om du väljer **Återställ**går valet för tidsintervall förlorade. Välj **test fråga** för att testa frågan och granska resultaten på fliken **test resultat** .

10. När du gör ändringar i frågan väljer du **Spara fråga** för att testa den nya fråge logiken. Detta gör att du kan ändra din fråga upprepade gånger och testa den igen för att se hur utdata ändras.

11. När du har kontrollerat resultaten som visas i webbläsaren är du redo att **Starta** jobbet.

## <a name="upload-sample-data-from-a-local-file"></a>Ladda upp exempel data från en lokal fil

I stället för att använda real tids data kan du använda exempel data från en lokal fil för att testa din Azure Stream Analytics fråga.

1. Logga in på Azure Portal.
   
2. Leta upp ditt befintliga Stream Analytics-jobb och markera den.

3. På sidan Stream Analytics jobb går du till rubriken **jobb Topology** och väljer **fråga** för att öppna fönstret Frågeredigeraren.

4. Om du vill testa din fråga med en lokal fil väljer du **Ladda upp exempel indata** på fliken **förhands granskning av indata** . 

   ![Azure Stream Analytics Ladda upp exempel fil](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Överför din lokala fil för att testa frågan. Du kan bara ladda upp filer med JSON-, CSV-eller AVRO-format. Välj **OK**.

   ![Azure Stream Analytics Ladda upp exempel fil](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. När du överför filen kan du också se filens innehåll i formuläret som en tabell eller i formatet RAW. Om du väljer **Återställ**kommer exempel data att återgå till den inkommande inmatnings informationen i föregående avsnitt. Du kan ladda upp andra filer för att testa frågan när som helst.

7. Välj **test fråga** för att testa din fråga mot den uppladdade exempel filen.

8. Test resultaten visas baserat på din fråga. Du kan ändra frågan och välja **Spara fråga** för att testa den nya fråge logiken. Detta gör att du kan ändra din fråga upprepade gånger och testa den igen för att se hur utdata ändras.

9. När du använder flera utdata i frågan visas resultaten baserat på valda utdata. 

   ![Azure Stream Analytics valda utdata](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. När du har kontrollerat resultaten som visas i webbläsaren kan du **Starta** jobbet.

## <a name="next-steps"></a>Nästa steg

* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Fråge exempel för vanliga Stream Analytics användnings mönster](stream-analytics-stream-analytics-query-patterns.md)

* [Förstå indata för Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md)