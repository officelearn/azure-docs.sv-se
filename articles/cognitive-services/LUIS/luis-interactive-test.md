---
title: Testa appen i LUIS-portalen
description: Använd Språkförståelse (LUIS) för att fungera kontinuerligt för ditt program för att förfina och förbättra dess språkförståelse.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361157"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testa din LUIS-app i LUIS-portalen

[Testning](luis-concept-test.md) av en app är en iterativ process. Testa den efter utbildning LUIS-appen, med exempel yttranden om avsikter och entiteter identifieras korrekt. Om de inte gör uppdateringar till LUIS-app, träna och testa igen.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Träna innan du testar

För att testa mot den senaste versionen av den aktiva appen väljer du **träna** på den översta menyn innan du testar.

## <a name="test-an-utterance"></a>Testa ett uttryck

Test-uttryck bör inte vara exakt samma som alla exempel-yttranden i appen. Test uttryck bör omfatta val av ord, fras längd och enhets användning som du förväntar dig för en användare.

1. Öppna din app genom att välja namnet på sidan **Mina appar** .

1. Välj **testa** i programmets övre panel för att komma åt **test** -bildspel-panelen.

    > [!div class="mx-imgBorder"]
    > Sidan ![träna & testa appen](./media/luis-how-to-interactive-test/test.png)

1. Ange ett uttryck i textrutan och tryck på RETUR. Du kan ange så många test-yttranden som du vill i **testet**, men bara en uttryck i taget.

1. Uttryck, dess främsta syftet och poäng läggs till i listan över yttranden under textrutan.

    ![Interaktiv testning identifierar fel avsikten](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Granska resultatet

Du har granskat informationen om test resultatet i **inspektions** panelen.

1. Med **testskärms** panelen öppen väljer du **inspektera** för en uttryck som du vill jämföra.

    ![Klicka på granska om du vill visa mer information om test resultaten](./media/luis-how-to-interactive-test/inspect.png)

1. **Inspektions** panelen visas. Panelen visas den översta bedömning avsikt, samt alla identifierade entiteter. På panelen visas resultatet av den valda uttryck.

    ![Panelen visas den översta bedömning avsikt, samt alla identifierade entiteter. På panelen visas resultatet av den valda uttryck.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Rätt upp bedömning avsikt

1. Om den översta bedömnings avsikten är felaktig väljer du knappen **Redigera** .

1.  I listrutan, väljer du den rätta uttryck som används.

    ![Välj rätt avsikt](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Visa sentiment resultat

Om **sentiment-analysen** har kon figurer ATS på **[publicerings](luis-how-to-publish-app.md#enable-sentiment-analysis)** sidan, inkluderar test resultatet sentiment som finns i uttryck.

![Bild av Testfönster med attitydanalys](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Korrigera matchat mönster avsikt

Om du använder [mönster](luis-concept-patterns.md) och uttryck har matchat ett mönster, men fel avsikten är förväntat, väljer du länken **Redigera** med mönstret och väljer sedan rätt avsikt.

## <a name="compare-with-published-version"></a>Jämför med publicerad version

Du kan testa den aktiva versionen av din app med den publicerade [slut punkts](luis-glossary.md#endpoint) versionen. I panelen **Granska** väljer du **Jämför med publicerad**. All testning mot publicerade modellen dras från din kvot saldo för Azure-prenumeration.

![Jämför med publiceras](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Visa endpoint JSON i test-panelen
Du kan visa slut punkts-JSON som returneras för jämförelsen genom att välja vyn **Visa JSON**.

![Publicerade JSON-svar](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Ytterligare inställningar i panelen för testning

### <a name="luis-endpoint"></a>LUIS-slutpunkt

Om du har flera LUIS-slutpunkter använder du länken **ytterligare inställningar** i testens publicerade fönster för att ändra slut punkten som används för testning. Om du inte är säker på vilken slut punkt du ska använda väljer du standard **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![test panel med länken ytterligare inställningar markerat](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Batch-testning
Se batch testing [Concepts](luis-concept-batch-test.md) och lär dig [hur du](luis-how-to-batch-test.md) testar en batch med yttranden.

## <a name="next-steps"></a>Nästa steg

Om testningen visar att LUIS-appen inte kan identifiera rätt avsikter och entiteter, kan du arbeta för att förbättra din LUIS-app noggrannhet genom märkning mer yttranden eller lägga till funktioner.

* [Märk föreslagna yttranden med LUIS](luis-how-to-review-endpoint-utterances.md)
* [Använd funktioner för att förbättra LUIS-appens prestanda](luis-how-to-add-features.md)
