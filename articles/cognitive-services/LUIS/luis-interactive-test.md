---
title: Testa appen i LUIS-portalen
description: Använd Language Understanding (LUIS) för att kontinuerligt arbeta med ditt program för att förfina det och förbättra dess språkförståelse.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219823"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testa LUIS-appen i LUIS-portalen

[Att testa](luis-concept-test.md) en app är en iterativ process. När du har tränat LUIS-appen testar du den med exempelyttranden för att se om avsikter och entiteter känns igen korrekt. Om de inte är det gör du uppdateringar av LUIS-appen, tränar och testar igen.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Träna före provning

Om du vill testa mot den senaste versionen av den aktiva appen väljer du **Träna** på den övre menyn innan du testar.

## <a name="test-an-utterance"></a>Testa ett uttryck

Testutseendet bör inte vara exakt samma som alla exempelyttranden i appen. Testutlysning bör innehålla ordval, fraslängd och entitetsanvändning som du förväntar dig för en användare.

1. Öppna appen genom att välja dess namn på sidan **Mina appar.**

1. Om du vill komma åt panelen **Testa** utbild väljer du **Testa** på programmets övre panel.

    > [!div class="mx-imgBorder"]
    > ![Sidan Träna & testapp](./media/luis-how-to-interactive-test/test.png)

1. Ange ett uttryck i textrutan och välj Retur. Du kan skriva så många testyttranden som du vill i **testet**, men bara ett uttryck i taget.

1. Uttrycket, dess högsta avsikt och poäng läggs till i listan över yttranden under textrutan.

    ![Interaktiv testning identifierar fel avsikt](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Kontrollera poäng

Du kontrollerar information om **Inspect** testresultatet i kontrollpanelen.

1. När panelen **Testa** utbild är öppen väljer du **Kontrollera** om det finns ett uttryck som du vill jämföra.

    ![Välj knappen Granska om du vill se mer information om testresultaten](./media/luis-how-to-interactive-test/inspect.png)

1. **Inspektionspanelen** visas. Panelen innehåller den högsta poängavsikten samt alla identifierade entiteter. Panelen visar resultatet av det valda uttrycket.

    ![Panelen innehåller den högsta poängavsikten samt alla identifierade entiteter. Panelen visar resultatet av det valda uttrycket.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Korrigera högsta poängavsikt

1. Om den högsta poängmetoden är felaktig väljer du knappen **Redigera.**

1.  I listrutan väljer du rätt avsikt för uttryck.

    ![Välj rätt avsikt](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Visa sentimentresultat

Om **sentimentanalys** är konfigurerad på sidan **[Publicera](luis-how-to-publish-app.md#enable-sentiment-analysis)** innehåller testresultaten sentimentet som finns i uttrycket.

![Bild av testfönstret med sentimentanalys](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Rätt matchade mönster avsikt

Om du använder [Mönster](luis-concept-patterns.md) och uttrycket matchade ett mönster, men fel avsikt förutsågs, väljer du länken **Redigera** efter mönstret och väljer sedan rätt avsikt.

## <a name="compare-with-published-version"></a>Jämför med publicerad version

Du kan testa den aktiva versionen av appen med den publicerade [slutpunktsversionen.](luis-glossary.md#endpoint) Välj Jämför med **publicerat**på panelen **Inspektera** . Alla tester mot den publicerade modellen dras av från ditt Azure-prenumerationskvotsaldo.

![Jämför med publicerade](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Visa slutpunkt JSON på testpanelen
Du kan visa slutpunkten JSON som returneras för jämförelsen genom att välja **visa JSON-vyn**.

![Publicerat JSON-svar](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Ytterligare inställningar på testpanelen

### <a name="luis-endpoint"></a>LUIS-slutpunkt

Om du har flera LUIS-slutpunkter använder du länken **Ytterligare inställningar** i fönstret Testa publicerad för att ändra slutpunkten som används för testning. Om du är osäker på vilken slutpunkt du ska använda väljer du standard **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![Testpanel med länken Ytterligare inställningar markerad](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Batch-testning
Se [batchtestkoncept](luis-concept-batch-test.md) och lär dig [hur](luis-how-to-batch-test.md) du testar en grupp yttranden.

## <a name="next-steps"></a>Nästa steg

Om testningen visar att LUIS-appen inte känner igen de rätta avsikter och entiteterna kan du arbeta för att förbättra LUIS-appens noggrannhet genom att märka fler yttranden eller lägga till funktioner.

* [Etikett föreslagna yttranden med LUIS](luis-how-to-review-endpoint-utterances.md)
* [Använda funktioner för att förbättra LUIS-appens prestanda](luis-how-to-add-features.md)
