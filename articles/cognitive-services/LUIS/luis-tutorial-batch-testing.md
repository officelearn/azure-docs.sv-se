---
title: 'Självstudiekurs: Batch-testning för att hitta problem - LUIS'
description: Den här självstudien visar hur du använder batchtestning för att validera kvaliteten på luis-appen (Language Understanding).
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78250485"
---
# <a name="tutorial-batch-test-data-sets"></a>Självstudiekurs: Batchtestdatauppsättningar

Den här självstudien visar hur du använder batchtestning för att validera kvaliteten på luis-appen (Language Understanding).

Med batchtestning kan du validera den aktiva, tränade modellens tillstånd med en känd uppsättning märkta yttranden och entiteter. I den JSON-formaterade kommandofilen lägger du till yttrandena och anger de entitetsetiketter som du behöver förutsägas i uttrycket.

Krav för batchprovning:

* Högst 1 000 yttranden per test.
* Inga dubbletter.
* Tillåtna entitetstyper: endast bearbetade inlärda entiteter.

När du använder en annan app än den här självstudien ska du *inte* använda de exempelyttranden som redan har lagts till i appen.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera exempelappen
> * Skapa en grupptestfil
> * Kör ett batchtest
> * Granska testresultat

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importera exempelappen

Importera en app som tar `1 pepperoni pizza on thin crust`en pizzabeställning, till exempel .

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Använd [förhandsgranskningen AV LUIS-portalen](https://preview.luis.ai/), importera JSON till en ny app, namnge appen `Pizza app`.

1. Välj **Träna** i det övre högra hörnet av navigeringen för att träna appen.

## <a name="what-should-the-batch-file-utterances-include"></a>Vad ska batch-filyttrandena innehålla

Kommandofilen ska innehålla yttranden med maskininlärda enheter på den högsta nivån som är märkta, inklusive start- och slutposition. Yttrandena bör inte vara en del av de exempel som redan finns i appen. De bör vara yttranden som du vill förutsäga positivt för avsikt och entiteter.

Du kan separera tester efter avsikt och/eller entitet eller ha alla tester (upp till 1 000 yttranden) i samma fil.

## <a name="batch-file"></a>Batch-fil

Exemplet JSON innehåller ett uttryck med en märkt entitet för att illustrera hur en testfil ser ut. I dina egna tester bör du ha många yttranden med rätt avsikt och maskininlärd entitet märkt.

1. Skapa `pizza-with-machine-learned-entity-test.json` i en textredigerare eller [ladda ner](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) den.

2. Lägg till ett uttryck med den **avsikt** som du vill ha förutsagt i testet i den JSON-formaterade kommandofilen.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Kör batchen

1. Välj **Testa** i det övre navigeringsfältet.

2. Välj **Batch-testpanel** på högerpanel.

3. Välj **Importera datauppsättning**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av LUIS-appen med importerad datauppsättning markerad](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Välj `pizza-with-machine-learned-entity-test.json` filens filplats.

5. Namnge datauppsättningen `pizza test` och välj **Klar**.

    > [!div class="mx-imgBorder"]
    > ![Välj fil](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Klicka på knappen **Kör**.

7. Välj **Visa resultat**.

8. Granska resultaten i diagrammet och förklaringen.

## <a name="review-batch-results-for-intents"></a>Granska batchresultat för avsikter

Testresultaten visar grafiskt hur testyttrandena förutsågs mot den aktiva versionen.

I batchdiagrammet visas fyra kvadranter av resultat. Till höger om diagrammet finns ett filter. Filtret innehåller avsikter och entiteter. När du markerar en [del av diagrammet](luis-concept-batch-test.md#batch-test-results) eller en punkt i diagrammet visas de associerade uttrycken nedanför diagrammet.

När du hovrar över diagrammet kan ett mushjul förstora eller minska visningen i diagrammet. Detta är användbart när det finns många punkter på diagrammet grupperade tätt tillsammans.

Diagrammet är i fyra kvadranter, med två av de avsnitt som visas i rött.

1. Välj **avsikten ModifyOrder** i filterlistan.

    > [!div class="mx-imgBorder"]
    > ![Välj Ändringsordermetod i filterlistan](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    Uttrycket förutspås som en **sann positiv** vilket innebär att uttrycket framgångsrikt matchade dess positiva förutsägelse som anges i kommandofilen.

    > [!div class="mx-imgBorder"]
    > ![Yttrandet matchade framgångsrikt dess positiva förutsägelse](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    De gröna bockarna i filterlistan anger också att testet lyckas för varje avsikt. Alla andra avsikter visas med en 1/1-positiv poäng eftersom uttrycket testades mot varje avsikt, som ett negativt test för alla avsikter som inte anges i batchtestet.

1. Välj **bekräftelseavsikten.** Den här avsikten visas inte i batchtestet så det här är ett negativt test av uttryck som anges i batchtestet.

    > [!div class="mx-imgBorder"]
    > ![Uttryck har förutspåtts negativt för olistad avsikt i batch-fil](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Det negativa testet lyckades, som noterats med den gröna texten i filtret och rutnätet.

## <a name="review-batch-test-results-for-entities"></a>Granska batchtestresultat för entiteter

Enheten ModifyOrder, som en datorentitet med underentiteter, visar om entiteten på den översta nivån matchas och hur underentiteterna förutses.

1. Markera entiteten **ModifyOrder** i filterlistan och välj sedan cirkeln i rutnätet.

1. Entitetsförutsägelsen visas under diagrammet. Displayen innehåller heldragna linjer för förutsägelser som matchar förväntningar och prickade linjer för förutsägelser som inte matchar förväntningarna.

    > [!div class="mx-imgBorder"]
    > ![Överordnad entitets överordnad i batchfilen](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Hitta fel med ett batchtest

Den här självstudien visade hur du kör ett test och tolkar resultat. Det täckte inte testfilosofi eller hur man ska svara på misslyckade tester.

* Se till att täcka både positiva och negativa yttranden i testet, inklusive yttranden som kan förutsägas för en annan men relaterad avsikt.
* För misslyckade yttranden utför du följande uppgifter och kör sedan testerna igen:
    * Granska aktuella exempel för avsikter och entiteter, validera exempelyttrandena för den aktiva versionen är korrekta både för avsikts- och entitetsmärkning.
    * Lägga till funktioner som hjälper appen att förutsäga avsikter och entiteter
    * Lägga till fler positiva exempelyttranden
    * Granska balans av exempelyttranden över avsikter

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Nästa steg

Självstudien använde ett batchtest för att validera den aktuella modellen.

> [!div class="nextstepaction"]
> [Läs mer om mönster](luis-tutorial-pattern.md)

