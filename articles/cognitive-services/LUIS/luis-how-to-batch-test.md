---
title: Batch testa appen THOMAS - Azure | Microsoft Docs
description: Använd språk förstå (THOMAS) batch testning för att hitta utterances med felaktigt avsikter och enheter.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 822fb1e2d5b13941527d242e8501b423bd6b81cb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265521"
---
# <a name="batch-testing"></a>Testa batch
 Testa batch är ett omfattande test på din aktuella tränad modell att mäta prestanda i THOMAS. 

<a name="batch-testing"></a>
## <a name="import-a-dataset-file-for-batch-testing"></a>Importera en dataset-fil för att testa batch

1. Välj **Test** i övre, och välj sedan **Batch-tester panelen**.

    ![Testa batch-länk](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Välj **importera dataset**. Den **Importera nya dataset** dialogrutan visas. Välj **Välj fil** och leta upp den [JSON](luis-concept-batch-test.md#batch-file-format) -fil som innehåller *mer än 1 000* utterances att testa.

    ![Importera Dataset-filen](./media/luis-how-to-batch-test/batchtest-importset.png)

    Importera fel rapporteras i en röd meddelandefält längst upp i webbläsaren. När en import har fel, skapas ingen datauppsättning. Mer information finns i [vanliga fel](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. I den **Dataset-namnet** , ange ett namn för dataset-fil. Dataset-filen innehåller en **matris med utterances** inklusive den *etikett avsikt* och *entiteter*. Granska de [exempel kommandofil](luis-concept-batch-test.md#batch-file-format) för syntax. 

4. Välj **klar**. Dataset-fil har lagts till.

## <a name="run-rename-export-or-delete-dataset"></a>Kör, byta namn på, exportera eller ta bort datauppsättning
Om du vill köra, byta namn på, exportera eller ta bort datauppsättningen, använder du de tre punkterna (**...** ) i slutet av raden dataset.

![DataSet-åtgärder](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Köra ett batch-test på utbildade appen

Välj dataset-namnet för att köra testet. När testet är klart visas den här raden testresultat för dataset.

![Testresultat för batch](./media/luis-how-to-batch-test/run-test.png)

Nedladdningsbar dataset är samma fil som laddades upp för batch-testning.

|Status|Betydelse|
|--|--|
|![Lyckad grön cirkel testikon](./media/luis-how-to-batch-test/batch-test-result-green.png)|Alla utterances är lyckas.|
|![Test röd x-ikon](./media/luis-how-to-batch-test/batch-test-result-red.png)|Minst en utterance avsikt matchade inte förutsägelser.|
|![Redo att testa ikon](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Testet är klart att köras.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>
## <a name="view-batch-test-results"></a>Visa testresultaten för batch 
Om du vill läsa igenom testresultaten batch, Välj **se resultatet**.

![Testresultat för batch](./media/luis-how-to-batch-test/run-test-results.png)

<!--
 Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. 

Green points indicate correct prediction, and red ones indicate incorrect prediction.

The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those items with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances.

-->


<a name="filter-chart-results-by-intent-or-entity"></a> ## Filtrera resultaten för diagram

Välj avsikt eller enhet på panelen höger filtrering för att filtrera diagram av en specifik avsikt eller enhet. Uppdatering i diagrammet enligt ditt val datapunkter och distribution. 
 
![Testresultat för visualiserade Batch](./media/luis-how-to-batch-test/filter-by-entity.png) 

<!--
## Investigate false sections
Data points on the **[False Positive][false-positive]** and **[False Negative][false-negative]** sections indicate errors, which should be investigated. If all data points are on the **[True Positive][true-positive]** and **[True Negative][true-negative]** sections, then your application's performance is perfect on this dataset.


The graph indicates [F-measure][f-measure], [recall][recall], and [precision][precision].  
-->
## <a name="view-single-point-utterance-data"></a>Visa enda punkt utterance data
Hovra över en datapunkt för att se säkerhet poängen för dess förutsägelse i diagrammet. Välj en datapunkt för att hämta dess motsvarande utterance i listan utterances längst ned på sidan. 

![Valda utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="view-section-data"></a>Visa avsnittet data
I diagrammet fyra avsnitt, Välj avsnittsnamn, exempelvis **falska positiva** på upp till höger om diagrammet. Under diagrammet visa alla utterances i avsnittet under diagrammet i en lista. 

![Valda utterances avsnittet](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

I föregående bilden, utterance `switch on` är märkt med TurnAllOn syfte, men tog emot förutsägelser ingen avsiktshantering. Det här är en indikation på att avsikten TurnAllOn måste flera exempel utterances för att kunna göra förväntade förutsägelse. 

Två avsnitt i diagrammet i rött anger utterances som inte matchade den förväntade förutsägelsen. De visar utterances vilka THOMAS behöver mer utbildning. 

Två avsnitt i diagrammet i grönt matchade den förväntade förutsägelsen.

## <a name="next-steps"></a>Nästa steg

Om testning visar att appen THOMAS inte kan identifiera rätt avsikter och enheter, kan du arbeta för att förbättra appen THOMAS prestanda genom etiketter mer utterances eller lägga till funktioner. 

* [Etiketten föreslagna utterances med THOMAS](Label-Suggested-Utterances.md) 
* [Använda funktioner för att förbättra appen THOMAS prestanda](luis-how-to-add-features.md) 
* [Förstå batch tester med den här självstudiekursen](luis-tutorial-batch-testing.md)
* [Lär dig testa begrepp batch](luis-concept-batch-test.md).

[true-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-positive
[true-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-negative
[false-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-positive
[false-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-negative
[f-measure]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#f-measure
[recall]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#recall
[precision]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#precision

