---
title: Batch testa appen THOMAS - Azure | Microsoft Docs
description: Använda batch testning kontinuerligt arbetar med ditt program att modifiera den och förbättra sin kunskap för språk.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 3803df32d6431b8413e8df0837ed62b2e4344cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353349"
---
# <a name="batch-testing-in-luis"></a>Testa i THOMAS batch

Testa batch verifierar din [active](luis-concept-version.md#active-version) tränade modellen att mäta dess prognosens noggrannhet kan förbättras. Med hjälp av ett batch-test kan du visa korrektheten i varje avsikt och entiteten i din aktuella tränad modell i ett diagram. Granska resultaten för att vidta lämpliga åtgärder för att förbättra Precision, till exempel lägga till fler exempel utterances syftet om din app ofta inte kan identifiera rätt avsikten batch.

## <a name="group-data-for-batch-test"></a>Gruppera data för batch
Det är viktigt att utterances som används för att testa batch har använt THOMAS. Om du har en datauppsättning för utterances dela utterances i tre olika: utterances som lagts till i en avsikt, utterances togs emot från publicerade slutpunkten och utterances som används för att testa batch THOMAS när den har installerats. 

## <a name="a-dataset-of-utterances"></a>En datauppsättning för utterances
Skicka en batchfil av utterances, kallas även en *dataset*, för att testa batch. Dataset är en JSON-formaterad fil som innehåller högst 1 000 märkta **icke-dubblett** utterances. I en app kan du testa upp till 10 datauppsättningar. Om du behöver testa flera ta bort en datauppsättning och sedan lägga till ett nytt.

|**Regler**|
|--|
|* Inga dubbla utterances|
|Inga underordnade hierarkiska entitet|
|1000 utterances eller mindre|

* Dubbletter betraktas som matchar exakt sträng, inte matchar är tokeniserad först. 

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>Batch-filformat
Kommandofilen består av utterances. Varje utterance måste ha en förväntade avsiktshantering förutsägelse tillsammans med någon [datorn inlärda entiteter](luis-concept-entity-types.md#types-of-entities) du förväntar dig att identifieras. 

Ett exempel på kommandofil följande:

   [!code-json[Valid batch test](~/samples-luis/documentation-samples/batch-testing/travel-agent-1.json)]


## <a name="common-errors-importing-a-batch"></a>Vanliga fel som importerar en batch
Vanliga fel är: 

> * Mer än 1 000 utterances
> * En utterance JSON-objekt som inte har en egenskap för enheter

## <a name="batch-test-state"></a>Batch testtillstånd
THOMAS spårar tillståndet för varje dataset senaste testet. Detta inkluderar datum för senaste körning storlek (antal utterances i batchen) och senaste resultat (antal har förväntade utterances).

<a name="sections-of-the-results-chart"></a>
## <a name="batch-test-results"></a>Testresultat för batch
Testresultat för batch är ett punktdiagram kallas en matris med fel. Det här diagrammet är ett 4-vägs jämförelse av utterances i filen och den aktuella modellen förväntade avsikt och enheter. 

Data som pekar på den **falska positiva** och **falska negativa** avsnitt visar fel, som bör undersökas. Om alla datapunkter är på den **SANT positivt** och **SANT negativt** avsnitten appens noggrannhet är perfekt för den här datauppsättningen.

![Fyra avsnitt i diagrammet](./media/luis-concept-batch-test/chart-sections.png)

Det här diagrammet hjälper dig att hitta utterances som beräknar THOMAS felaktigt baserat på dess aktuella utbildning. Resultatet visas per region i diagrammet. Välj enskilda felpunkter på diagrammet att granska informationen om utterance eller välj regionnamn och granska resultatet från utterance i den regionen.

![Testa batch](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Fel i resultaten
Fel i batch-testet visar avsikter som inte beräknas enligt beskrivningen i kommandofilen. Fel visas i rött två avsnitt i diagrammet. 

Avsnittet falska positiva anger att en utterance matchar en avsikt eller enhet när det inte borde ha. FALSKT negativt anger en utterance inte överensstämmer med en avsikt eller enhet när den ska ha. 

## <a name="fixing-batch-errors"></a>Korrigera batch-fel
Om det finns fel i batch-testning, du kan antingen lägga till flera utterances syftet och/eller etikett mer utterances med entiteten för att göra diskriminering avsikter THOMAS. Om du har lagt till utterances och märkta dem och fortfarande get förutsägelse fel i batch testning kan du lägga till en [frasen listan](luis-concept-feature.md) funktion med domänspecifika ordförråd att THOMAS Lär dig snabbare. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [testa en batch](luis-how-to-batch-test.md)