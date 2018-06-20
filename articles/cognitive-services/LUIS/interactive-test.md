---
title: Träna och testa appen THOMAS - Azure | Microsoft Docs
description: Använda språk förstå (THOMAS) kontinuerligt arbetar med ditt program att modifiera den och förbättra sin kunskap för språk.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: fb4c3bb117d1ea60c9cc28d2b193ee3c01f6c945
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221639"
---
# <a name="test-your-luis-app"></a>Testa appen THOMAS
<a name="train-your-app"></a>
[Utbildning](luis-how-to-train.md) och [testning](luis-concept-test.md) en app är en återkommande process. Testa den efter utbildning THOMAS appen med exempel utterances att se om avsikter och enheter identifieras korrekt. Om de inte göra uppdateringar till THOMAS appen, tåg och testa igen. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="interactive-testing"></a>
## <a name="test-an-utterance"></a>Testa en utterance

1. Åtkomst till din app genom att markera namnet på den **Mina appar** sidan. 

2. Att få åtkomst till den **Test** bild ut fönstret väljer **Test** i övre panelen för ditt program.

    ![Sidan tåg och testa appen](./media/luis-how-to-interactive-test/test.png)

3. Ange en utterance i textrutan och välj RETUR. Du kan skriva som många testa utterances som du vill ha i den **testa**, men bara en utterance i taget.

4. Utterance dess övre avsikt och poäng läggs till i listan över utterances under textrutan.

    ![Interaktiva testning identifierar fel avsikten](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="clear-test-panel"></a>Rensa test panelen
Om du vill rensa alla angivna test utterances samt resultaten från test-konsolen, Välj **börja om från början** i det övre vänstra hörnet på de **Test panelen**. 

## <a name="close-test-panel"></a>Stäng test panelen
Stäng den **Test** Kontrollpanelen, Välj den **Test** igen.

## <a name="inspect-score"></a>Inspektera poäng
Du kan granska information om testresultat i den **granska** panelen. 
 
1. Med den **Test** bild ut panelen är öppen väljer **granska** för en utterance som du vill jämföra. 

    ![Inspektera knappen](./media/luis-how-to-interactive-test/inspect.png)

2. Den **inspektion** panelen visas. Panelen innehåller upp bedömningen avsikt, samt alla identifierade enheter. På panelen visas resultatet av den valda utterance.

    ![Inspektera knappen](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Rätt upp bedömningen avsikt

1. Om upp bedömningen avsikt är felaktig, väljer den **redigera** knappen.

2.  Välj rätt avsett för utterance i den nedrullningsbara listan.

    ![Välj rätt avsikt](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Visa sentiment resultat

Om **Sentiment analysis** har konfigurerats på den **[publicera](publishapp.md#enable-sentiment-analysis)** sidan testresultaten inkluderar sentiment hittades i utterance. 

![Bild av Test rutan med sentiment analys](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Korrigera matchade mönstret avsikt
Om du använder [mönster](luis-concept-patterns.md) och utterance matchar ett mönster, men fel avsikten var förutsade, Välj den **redigera** länka av mönstret och sedan välja rätt avsikten.

## <a name="compare-with-published-version"></a>Jämför med publicerade version
Du kan testa den aktiva versionen av appen med det publicerade [endpoint](luis-glossary.md#endpoint) version. I den **granska** fönstret väljer **Jämför med publicerade**. All testning mot modellen för publicerade dras från ditt Azure-prenumeration kvoten saldo. 

![Jämför med publicerade](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Visa endpoint JSON test Kontrollpanelen
Du kan visa slutpunkten JSON returneras för jämförelse genom att välja den **visa JSON visa**.

![Publicerade JSON-svar](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Ytterligare inställningar test Kontrollpanelen

### <a name="luis-endpoint"></a>THOMAS slutpunkt
Om du har flera THOMAS slutpunkter, Använd den **ytterligare inställningar** länk på testet s publicerade fönstret om du vill ändra den slutpunkt som används för att testa. Välj standard om du inte är säker på vilken slutpunkt för att använda **Starter_Key**. 

![Testa panel med ytterligare inställningslänken markerad](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Visa Bing stavningskontroll korrigeringar test Kontrollpanelen
Krav för att visa stavning ändringar: 

* Publicerade app
* Bing stavningskontroll [tjänstnyckeln](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). Nyckeln för tjänsten lagras inte och måste återställas för varje webbläsarsession. 

Använd följande procedur för att inkludera den [Bing stavningskontroll v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) tjänsten i rutan testresultaten. 

1. I den **Test** rutan Ange ett utterance. När utterance förutsade, välja **[granska](#inspect-score)** under utterance som du angav. 

2. När den **granska** panelen öppnas väljer  **[Jämför med publicerade](#compare-with-published-version)**. 

3. När den **publicerade** panelen öppnas väljer  **[ytterligare inställningar](#additional-settings-in-test-panel)**.

4. I popup-fönstret, anger du ditt **Bing stavningskontroll** nyckeln för tjänsten. 
    ![Ange nyckel för Bing stavningskontroll](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

5. Ange en fråga med en felaktig stavning som `book flite to seattle` och välj Ange. Felaktig stavningen av ordet `flite` ersätts i frågan som skickas till THOMAS och resulterande JSON visas både den ursprungliga frågan som `query`, och korrigerade stavningen i frågan, som `alteredQuery`.

    ![Åtgärdat stavning JSON](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

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
## <a name="batch-testing"></a>Testa batch
Se batch testar [begrepp](luis-concept-batch-test.md) och lära dig [så](luis-how-to-batch-test.md) testa en batch med utterances.

## <a name="next-steps"></a>Nästa steg

Om testning visar att appen THOMAS inte kan identifiera rätt avsikter och enheter, kan du arbeta för att förbättra appen THOMAS noggrannhet genom etiketter mer utterances eller lägga till funktioner. 

* [Etiketten föreslagna utterances med THOMAS](Label-Suggested-Utterances.md) 
* [Använda funktioner för att förbättra appen THOMAS prestanda](luis-how-to-add-features.md) 
