---
title: Testa appen i LUIS-portalen
titleSuffix: Azure Cognitive Services
description: Använd Språkförståelse (LUIS) för att fungera kontinuerligt för ditt program för att förfina och förbättra dess språkförståelse.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31d979b92bdf050f28ac5d7549218016dc5e473c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638019"
---
<a name="train-your-app"></a>

# <a name="test-your-luis-app-in-the-luis-portal"></a>Testa din LUIS-app i LUIS-portalen

[Testning](luis-concept-test.md) av en app är en iterativ process. Testa den efter utbildning LUIS-appen, med exempel yttranden om avsikter och entiteter identifieras korrekt. Om de inte gör uppdateringar till LUIS-app, träna och testa igen. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Testa ett uttryck

1. Få åtkomst till din app genom att välja dess namn på den **Mina appar** sidan. 

1. Åtkomst till den **Test** bild ut fönstret väljer **Test** i övre panelen för ditt program.

    ![Träna och testa appen](./media/luis-how-to-interactive-test/test.png)

1. Ange ett uttryck i textrutan och tryck på RETUR. Du kan skriva som många testa yttranden som du vill ha i den **testa**, men endast ett uttryck i taget.

1. Uttryck, dess främsta syftet och poäng läggs till i listan över yttranden under textrutan.

    ![Interaktiv testning identifierar fel avsikten](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Granska resultatet

Du kan se mer information om testresultat i den **granska** panelen. 
 
1. Med den **Test** bild ut panelen öppen, väljer **granska** för ett uttryck som du vill jämföra. 

    ![Klicka på granska om du vill visa mer information om test resultaten](./media/luis-how-to-interactive-test/inspect.png)

1. Den **inspektion** visas panelen. Panelen visas den översta bedömning avsikt, samt alla identifierade entiteter. På panelen visas resultatet av den valda uttryck.

    ![Panelen visas den översta bedömning avsikt, samt alla identifierade entiteter. På panelen visas resultatet av den valda uttryck.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Rätt upp bedömning avsikt

1. Om upp bedömning avsikten är felaktig, väljer du den **redigera** knappen.

1.  I listrutan, väljer du den rätta uttryck som används.

    ![Välj rätt avsikt](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Visa sentiment resultat

Om **attitydanalys** har konfigurerats på den **[publicera](luis-how-to-publish-app.md#enable-sentiment-analysis)** sidan testresultaten inkluderar attityden hittades i uttryck. 

![Bild av Testfönster med attitydanalys](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Korrigera matchat mönster avsikt

Om du använder [mönster](luis-concept-patterns.md) och uttryck som matchar ett mönster, men fel avsikten var förutse, Välj den **redigera** länka efter användningsmönster, och väljer sedan rätt avsikten.

## <a name="compare-with-published-version"></a>Jämför med publicerad version

Du kan testa den aktiva versionen av din app med det publicerade [endpoint](luis-glossary.md#endpoint) version. I den **granska** fönstret väljer **Jämför med publicerade**. All testning mot publicerade modellen dras från din kvot saldo för Azure-prenumeration. 

![Jämför med publiceras](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Visa endpoint JSON i test-panelen
Du kan visa den slutpunkt som JSON returneras för jämförelsen genom att välja den **visa JSON-vy**.

![Publicerade JSON-svar](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Ytterligare inställningar i panelen för testning

### <a name="luis-endpoint"></a>LUIS-slutpunkt

Om du har flera LUIS-slutpunkter kan använda den **ytterligare inställningar** länk på testet s publicerade fönstret om du vill ändra den slutpunkt som används för testning. Om du inte är säker på vilken slutpunkt som ska användas, väljer du standard **Starter_Key**. 

![Testa panelen med ytterligare inställningar med länken markerad](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Visa ändringar för stavningskontroll i Bing i test-panelen

Krav för att visa stavningskorrigeringar: 

* Publicerad app
* Stavningskontroll i Bing [tjänstnyckeln](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). Tjänstnyckeln lagras inte och måste startas om för varje webbläsarsession. 

Använd följande procedur för att inkludera den [stavningskontroll i Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) tjänsten på resultaten av fönstret. 

1. I den **Test** rutan Ange ett uttryck. När uttryck förväntas, välja **[granska](#inspect-score)** under uttryck som du har angett. 

1. När den **granska** panelen öppnas, Välj  **[Jämför med publicerad](#compare-with-published-version)** . 

1. När den **publicerad** panelen öppnas, Välj  **[ytterligare inställningar](#additional-settings-in-test-panel)** .

1. I popup-dialog rutan markerar du **aktivera stavningskontroll i Bing** och anger nyckeln och väljer sedan **Slutför**. 
    ![Ange nyckel för tjänstens stavningskontroll i Bing](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Ange en fråga med en felaktig stavning som `book flite to seattle` och välj Ange. Felaktig stavningen av ordet `flite` ersätts i den fråga som skickats till LUIS och den resulterande JSON visar både den ursprungliga frågan som `query`, och korrigerade stavningen i frågan, som `alteredQuery`.

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="batch-testing"></a>Batch-testning
Se batch testning [begrepp](luis-concept-batch-test.md) och lär dig [så](luis-how-to-batch-test.md) testa en batch med yttranden.

## <a name="next-steps"></a>Nästa steg

Om testningen visar att LUIS-appen inte kan identifiera rätt avsikter och entiteter, kan du arbeta för att förbättra din LUIS-app noggrannhet genom märkning mer yttranden eller lägga till funktioner. 

* [Etiketten föreslagna yttranden med LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Använda funktioner för att förbättra prestanda för dina LUIS-app](luis-how-to-add-features.md) 
