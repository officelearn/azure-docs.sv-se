---
title: Batch testa LUIS-appen – Azure | Microsoft Docs
description: Använd Språkförståelse (LUIS) batch testning för att hitta yttranden med felaktig avsikter och entiteter.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: ec023ad796c57141cb35cce15b3d982adf2b8659
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887767"
---
# <a name="batch-testing"></a>Batch-testning
 Testning av batch är en omfattande test på din aktuella tränade modellen att mäta prestanda i LUIS. 

<a name="batch-testing"></a>
## <a name="import-a-dataset-file-for-batch-testing"></a>Importera en datauppsättning-fil för att testa batch

1. Välj **Test** längst upp och sedan väljer **Batch-testning panelen**.

    ![Batch Testa länk](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Välj **importera datauppsättningen**. Den **importera ny datauppsättning** dialogrutan visas. Välj **Välj fil** och leta upp en JSON-fil med rätt [JSON-format](luis-concept-batch-test.md#batch-file-format) som innehåller *mer än 1 000* yttranden att testa.

    ![Importera datauppsättningen fil](./media/luis-how-to-batch-test/batchtest-importset.png)

    Importfel rapporteras i en röd meddelandefältet längst upp i webbläsaren. När en import har fel, skapas inte någon datauppsättning. Mer information finns i [vanliga fel](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. I den **Datamängdsnamn** fältet, anger du ett namn för din datauppsättning. Dataset-filen innehåller en **matris med yttranden** , inklusive den *märkta avsikt* och *entiteter*. Granska den [batch-exempelfil](luis-concept-batch-test.md#batch-file-format) syntax. 

4. Välj **Done** (Klar). Datauppsättningsfilen har lagts till.

## <a name="run-rename-export-or-delete-dataset"></a>Kör, byta namn på, exportera eller ta bort datauppsättning
Om du vill köra, byta namn på, exportera eller ta bort datauppsättningen, använder du de tre punkterna (***...*** ) i slutet av raden för datauppsättningen.

![Åtgärder för datauppsättning](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Köra ett batch-test på din tränade app

Kör testet genom att markera datauppsättningens namn. När testet är klart visar den här raden testresultatet av datauppsättningen.

![Testresultat för batch](./media/luis-how-to-batch-test/run-test.png)

Nedladdningsbara datauppsättningen är samma fil som har överförts för att testa batch.

|Status|Betydelse|
|--|--|
|![Testet lyckats grön cirkel ikon](./media/luis-how-to-batch-test/batch-test-result-green.png)|Alla yttranden har lyckats.|
|![Misslyckade test röda x-ikonen](./media/luis-how-to-batch-test/batch-test-result-red.png)|Minst ett uttryck avsikt matchade inte förutsägelser.|
|![Redo att testa ikon](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Testning är redo att köra.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>
## <a name="view-batch-test-results"></a>Visa testresultat för batch 
Om du vill granska resultaten av batch, Välj **se resultat**.

![Testresultat för batch](./media/luis-how-to-batch-test/run-test-results.png)

<!--
 Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. 

Green points indicate correct prediction, and red ones indicate incorrect prediction.

The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those items with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances.

-->


<a name="filter-chart-results-by-intent-or-entity"></a> ## Filtrera diagrammet resultat

Välj avsikt eller entitet i panelen höger filtrering för att filtrera diagrammet efter ett specifikt syfte eller en enhet. Uppdatera i diagrammet enligt ditt val av datapunkter och deras distribution. 
 
![Testresultat för visualiserade Batch](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Visa enda punkt uttryck data
I diagrammet, hovrar du över en datapunkt för att se säkerhet poängen för dess förutsägelse. Välj en datapunkt för att hämta dess motsvarande uttryck i listan yttranden längst ned på sidan. 

![Valda uttryck](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="view-section-data"></a>Visa avsnittet data
I diagrammet är fyra avsnitt, välja avsnittsnamn, till exempel **falskt positivt** på upp till höger i diagrammet. Under diagrammet visas alla uttryck i avsnittet under diagrammet i en lista. 

![Valda yttranden avsnittet](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

I den här föregående bild, uttryck `switch on` är märkt med TurnAllOn avsikt, men tog emot förutsägelsen NONE avsikt. Detta är en indikation om att TurnAllOn avsikten måste flera exempel yttranden för att göra den förväntade förutsägelsen. 

De två avsnitten i diagrammet i rött anger yttranden som inte matchade den förväntade förutsägelsen. De visar yttranden vilka LUIS behöver mer utbildning. 

De två avsnitten i diagrammet i grönt matchade den förväntade förutsägelsen.

## <a name="next-steps"></a>Nästa steg

Om testningen visar att LUIS-appen inte kan identifiera rätt avsikter och entiteter, kan du arbeta för att förbättra prestanda för dina LUIS-app genom märkning mer yttranden eller lägga till funktioner. 

* [Etiketten föreslagna yttranden med LUIS](Label-Suggested-Utterances.md) 
* [Använda funktioner för att förbättra prestanda för dina LUIS-app](luis-how-to-add-features.md) 
* [Förstå batch testning med den här självstudien](luis-tutorial-batch-testing.md)
* [Lär dig batch testning begrepp](luis-concept-batch-test.md).