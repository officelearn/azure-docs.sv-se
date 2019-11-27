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
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 628547e8254bb0055cf1f09af50e79b68311a759
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221726"
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

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Ytterligare inställningar i panelen för testning

### <a name="luis-endpoint"></a>LUIS-slutpunkt

Om du har flera LUIS-slutpunkter använder du länken **ytterligare inställningar** i testens publicerade fönster för att ändra slut punkten som används för testning. Om du inte är säker på vilken slut punkt du ska använda väljer du standard **Starter_Key**. 

> [!div class="mx-imgBorder"]
> ![test panel med länken ytterligare inställningar markerat](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)

<!--
###  View Bing Spell Check corrections in test panel

Requirements to view the spelling corrections: 

* Published app
* Bing Spell Check [service key](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). The service key is not stored and needs to be reset for each browser session. 

Use the following procedure to include the [Bing Spell Check v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) service  in the Test pane results. 

1. In the **Test** pane, enter an utterance. When the utterance is predicted, select **[Inspect](#inspect-score)** underneath the utterance you entered. 

1. When the **Inspect** panel opens, select **[Compare with Published](#compare-with-published-version)**. 

1. When the **Published** panel opens, select **[Additional Settings](#additional-settings-in-test-panel)**.

1. In the pop-up dialog, check **Enable Bing Spell Check** and enter the key, then select **Done**. 
    ![Enter Bing Spell Check service key](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Enter a query with an incorrect spelling such as `book flite to seattle` and select enter. The incorrect spelling of the word `flite` is replaced in the query sent to LUIS and the resulting JSON shows both the original query, as `query`, and the corrected spelling in the query, as `alteredQuery`.

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
-->

## <a name="batch-testing"></a>Batch-testning
Se batch testing [Concepts](luis-concept-batch-test.md) och lär dig [hur du](luis-how-to-batch-test.md) testar en batch med yttranden.

## <a name="next-steps"></a>Nästa steg

Om testningen visar att LUIS-appen inte kan identifiera rätt avsikter och entiteter, kan du arbeta för att förbättra din LUIS-app noggrannhet genom märkning mer yttranden eller lägga till funktioner. 

* [Märk föreslagna yttranden med LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Använd funktioner för att förbättra LUIS-appens prestanda](luis-how-to-add-features.md) 
