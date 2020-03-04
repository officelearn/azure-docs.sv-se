---
title: 'Självstudie: batch-testning för att hitta problem – LUIS'
description: Den här självstudien visar hur du använder batch-testning för att verifiera kvaliteten på din Language Understanding-app (LUIS).
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250485"
---
# <a name="tutorial-batch-test-data-sets"></a>Självstudie: data uppsättningar för batch-test

Den här självstudien visar hur du använder batch-testning för att verifiera kvaliteten på din Language Understanding-app (LUIS).

Batch-testning kan du kontrollera aktiva tränas modellens tillstånd med en känd uppsättning av taggade yttranden och entiteter. I JSON-formaterade kommandofilen, Lägg till talade och ange etiketter för entiteten som du behöver förutse inuti uttryck.

Krav för att testa batch:

* Högst 1000 yttranden per test.
* Inga dubbletter.
* Tillåtna entitetstyper: endast enheter som har sparats i enheten.

När du använder en annan app än den här själv studie kursen ska du *inte* använda exemplet yttranden som redan har lagts till i din app.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera en exempelapp
> * Skapa en batchfil för testning
> * Köra ett batch-test
> * Granskningsresultat

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importera en exempelapp

Importera en app som tar en pizza-ordning, till exempel `1 pepperoni pizza on thin crust`.

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Använd LUIS-portalen för för [hands versionen](https://preview.luis.ai/)för att importera JSON till en ny app, namnge appen `Pizza app`.

1. Välj **träna** i det övre högra hörnet i navigeringen för att träna appen.

## <a name="what-should-the-batch-file-utterances-include"></a>Vad ska kommando filen yttranden innehålla

Kommando filen bör innehålla yttranden med de översta enhets enheter som är märkta, inklusive start-och slut position. Yttranden ska inte ingå i exemplen som redan finns i appen. De bör vara yttrandena att du vill förutsäga för avsikt och entiteter positivt.

Du kan separera tester efter avsikt och/eller entitet eller ha alla test (upp till 1000 yttranden) i samma fil.

## <a name="batch-file"></a>Kommandofil

JSON-exemplet innehåller en uttryck med en etikettad entitet som illustrerar vad en testfil ser ut. I dina egna tester bör du ha många yttranden med rätt namngivnings avsikt och enhet som har registrerats av enheten.

1. Skapa `pizza-with-machine-learned-entity-test.json` i en text redigerare eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) den.

2. I den JSON-formaterade kommando filen lägger du till en uttryck med den **avsikt** som du vill förutsäga i testet.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Kör om gruppen

1. Välj **test** i det övre navigerings fältet.

2. Välj **panelen för batch-testning** på den högra panelen.

3. Välj **Importera data uppsättning**.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av LUIS-appen med importerad data uppsättning markerat](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Välj fil platsen för `pizza-with-machine-learned-entity-test.json`s filen.

5. Namnge data uppsättningen `pizza test` och välj **färdig**.

    > [!div class="mx-imgBorder"]
    > ![Välj fil](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Klicka på knappen **Kör**.

7. Välj **se resultat**.

8. Granska resultatet i diagram och legend.

## <a name="review-batch-results-for-intents"></a>Granska resultat från batch

Test resultaten visar grafiskt hur test yttranden förutsägdes mot den aktiva versionen.

Batch-diagrammet visar fyra quadrants resultat. Är ett filter till höger om diagrammet. Filtret innehåller avsikter och entiteter. När du väljer en [del av diagrammet](luis-concept-batch-test.md#batch-test-results) eller en punkt i diagrammet visas tillhör ande uttryck under diagrammet.

Vid hovring över diagrammet, ett mushjul förstora eller minska visas i diagrammet. Detta är användbart när det finns många saker i diagrammet klustrade nära tillsammans.

Diagrammet är i fyra quadrants med två av de avsnitt som visas i rött.

1. Välj **ModifyOrder** avsikt i filter listan.

    > [!div class="mx-imgBorder"]
    > ![Välj ModifyOrder avsikt från filter listan](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    Uttryck förutsägs som en **sann positiv** betydelse för uttryck som har matchat sin positiva förutsägelse i kommando filen.

    > [!div class="mx-imgBorder"]
    > ![uttryck har matchat sin positiva förutsägelse](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    De gröna bockarna i filter listan visar också testets framgång för varje avsikt. Alla andra avsikter visas med en 1/1 positiv Poäng eftersom uttryck har testats för varje avsikt, som ett negativt test för alla syften som inte listas i batch-testet.

1. Välj **bekräftelse** avsikt. Avsikten visas inte i batch-testet, så det är ett negativt test av uttryck som anges i batch-testet.

    > [!div class="mx-imgBorder"]
    > ![uttryck har förutsägt negativt för en lista med registrerade syften i kommando filen](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Det negativa testet lyckades, som det skrevs med den gröna texten i filtret och rutnätet.

## <a name="review-batch-test-results-for-entities"></a>Granska resultat för batch-test för entiteter

Entiteten ModifyOrder, som en dator entitet med underentiteter, visar om entiteten på den översta nivån är matchad och visar hur underentiteterna förutsägs.

1. Välj entiteten **ModifyOrder** i filter listan och välj sedan cirkeln i rutnätet.

1. Entitetens förutsägelse visas under diagrammet. Skärmen innehåller heldragna linjer för förutsägelser som matchar förväntad och prickade rader för förutsägelser som inte matchar förväntat.

    > [!div class="mx-imgBorder"]
    > den överordnade ![entiteten har förväntats i kommando filen](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Hitta fel med ett batch-test

I den här självstudien visar vi hur du kör ett test och tolkar resultat. Det fick inte test filosofin eller svara på misslyckade tester.

* Se till att ta med både positiva och negativa yttranden i testet, inklusive yttranden som kan förutsägas för en annan men relaterad avsikt.
* För att yttranden ska du utföra följande aktiviteter och sedan köra testerna igen:
    * Granska aktuella exempel för avsikter och entiteter. kontrol lera att yttranden för den aktiva versionen är rätt både för avsikts-och entitets etiketter.
    * Lägg till funktioner som hjälper ditt program att förutsäga avsikter och entiteter
    * Lägg till mer positiv exempel yttranden
    * Granska saldot för exempel yttranden över avsikter

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Nästa steg

Självstudien använde ett batch-test för att validera den aktuella modellen.

> [!div class="nextstepaction"]
> [Lär dig mer om mönster](luis-tutorial-pattern.md)

