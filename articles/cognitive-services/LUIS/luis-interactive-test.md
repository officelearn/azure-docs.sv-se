---
title: Testa appen i LUIS-portalen
description: Använd Language Understanding (LUIS) för att kontinuerligt arbeta med ditt program för att förfina det och förbättra dess språk förståelse.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: cb4559e6d3582a337cbc32ef986bec5db5940ef9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591870"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testa din LUIS-app i LUIS-portalen

[Testning](luis-concept-test.md) av en app är en iterativ process. När du har tränat din LUIS-app kan du testa den med exempel yttranden för att se om intentarna och entiteterna identifieras korrekt. Om de inte är det kan du göra uppdateringar till LUIS-appen, träna och testa igen.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Träna innan du testar

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. För att testa mot den senaste versionen av den aktiva appen väljer du **träna** på den översta menyn innan du testar.

## <a name="test-an-utterance"></a>Testa en uttryck

Test-uttryck bör inte vara exakt samma som alla exempel-yttranden i appen. Test uttryck bör omfatta val av ord, fras längd och enhets användning som du förväntar dig för en användare.

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .

1. Välj **testa** i programmets övre panel för att komma åt **test** -bildspel-panelen.

    > [!div class="mx-imgBorder"]
    > ![Sidan träna & test app](./media/luis-how-to-interactive-test/test.png)

1. Ange en uttryck i text rutan och välj RETUR. Du kan ange så många test-yttranden som du vill i **testet**, men bara en uttryck i taget.

1. Uttryck, dess främsta avsikt och score läggs till i listan över yttranden under text rutan.

    ![Interaktiv testning identifierar fel avsikt](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Granska Poäng

Du har granskat informationen om test resultatet i **inspektions** panelen.

1. Med **testskärms** panelen öppen väljer du **inspektera** för en uttryck som du vill jämföra.

    ![Klicka på granska om du vill visa mer information om test resultaten](./media/luis-how-to-interactive-test/inspect.png)

1. **Inspektions** panelen visas. Panelen innehåller den översta bedömnings avsikten samt alla identifierade entiteter. Panelen visar resultatet av den valda uttryck.

    ![Panelen innehåller den översta bedömnings avsikten samt alla identifierade entiteter. Panelen visar resultatet av den valda uttryck.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Korrigera topp i resultat syfte

1. Om den översta bedömnings avsikten är felaktig väljer du knappen **Redigera** .

1.  Välj rätt avsikt för uttryck i list rutan.

    ![Välj rätt avsikt](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Visa sentiment-resultat

Om **sentiment-analysen** har kon figurer ATS på **[publicerings](luis-how-to-publish-app.md#enable-sentiment-analysis)** sidan, inkluderar test resultatet sentiment som finns i uttryck.

![Bild av test fönstret med sentiment-analys](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Korrekt matchat mönsters avsikt

Om du använder [mönster](luis-concept-patterns.md) och uttryck har matchat ett mönster, men fel avsikten är förväntat, väljer du länken **Redigera** med mönstret och väljer sedan rätt avsikt.

## <a name="compare-with-published-version"></a>Jämför med publicerad version

Du kan testa den aktiva versionen av din app med den publicerade [slut punkts](luis-glossary.md#endpoint) versionen. I panelen **Granska** väljer du **Jämför med publicerad**. Tester mot den publicerade modellen dras av från kvot balansen för Azure-prenumerationen.

![Jämför med publicerad](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Visa Endpoint JSON i test panelen
Du kan visa slut punkts-JSON som returneras för jämförelsen genom att välja vyn **Visa JSON**.

![Publicerat JSON-svar](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Ytterligare inställningar i test panelen

### <a name="luis-endpoint"></a>LUIS-slutpunkt

Om du har flera LUIS-slutpunkter använder du länken **ytterligare inställningar** i testens publicerade fönster för att ändra slut punkten som används för testning. Om du inte är säker på vilken slut punkt du ska använda väljer du standard **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![Panelen test med ytterligare inställningar länk markerad](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Batch-testning
Se batch testing [Concepts](luis-concept-batch-test.md) och lär dig [hur du](luis-how-to-batch-test.md) testar en batch med yttranden.

## <a name="next-steps"></a>Nästa steg

Om testning indikerar att LUIS-appen inte känner igen rätt avsikter och entiteter kan du arbeta för att förbättra din LUIS-appens exakthet genom att märka fler yttranden eller lägga till funktioner.

* [Märk föreslagna yttranden med LUIS](luis-how-to-review-endpoint-utterances.md)
* [Använd funktioner för att förbättra LUIS-appens prestanda](luis-how-to-add-features.md)
