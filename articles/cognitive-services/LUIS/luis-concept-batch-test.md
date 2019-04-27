---
title: Batch-testning
titleSuffix: Language Understanding - Azure Cognitive Services
description: Använda batch testning för att arbeta kontinuerligt för ditt program för att förfina och förbättra dess språkförståelse.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: acb561970b6a8576d1219fc15758e21a3032c9e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813292"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Batch testning med 1000 yttranden LUIS-portalen

Batch testning validerar din [active](luis-concept-version.md#active-version) tränade modellen att mäta dess prognosens noggrannhet kan förbättras. Ett batch-test kan du visa det arbete du utfört varje avsikt och entiteten i din aktuella tränade modellen i ett diagram. Granska resultaten av batch för att vidta lämpliga åtgärder för att förbättra noggrannhet, som att lägga till fler exempel yttranden till en avsikt om din app ofta inte kan identifiera rätt avsikten.

## <a name="group-data-for-batch-test"></a>Gruppera data för batch-test

Det är viktigt att yttranden som används för att testa batch har LUIS. Om du har en datauppsättning för yttranden dela yttranden i tre grupper: yttranden som lagts till i en avsikt, yttranden som togs emot från den publicerade slutpunkten och yttranden som används för att testa batch LUIS när den har installerats. 

## <a name="a-dataset-of-utterances"></a>En datauppsättning för yttranden

Skicka en kommandofil av yttranden som kallas en *datauppsättning*, för att testa batch. Datauppsättningen är en JSON-formaterad fil som innehåller högst 1 000 märkta **icke dubblett** yttranden. Du kan testa upp till 10 datauppsättningar i en app. Om du vill testa mer, ta bort en datauppsättning och sedan lägga till en ny.

|**Regler**|
|--|
|* Inga dubbla yttranden|
|1000 yttranden eller mindre|

* Dubbletter anses exakta strängen matchar, inte matchningar som är tokeniserad först. 

## <a name="entities-allowed-in-batch-tests"></a>Entiteter som tillåts i batch-tester

Alla anpassade entiteter i modellen visas i filtret batch test entiteter även om det finns inga motsvarande entiteter i batch-fildata.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Batch-filformat

Kommandofilen består av yttranden. Varje uttryck måste ha en förväntade avsikt förutsägelse tillsammans med eventuella [datorn lärt dig entiteter](luis-concept-entity-types.md#types-of-entities) du förväntar dig att identifieras. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Mall för batch-syntax för avsikter med entiteter

Använd följande mall för att starta kommandofilen:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Batchfilen använder den **startPos** och **endPos** egenskaper att notera i början och slutet av en entitet. Värdena får är nollbaserat och inte börja eller sluta på ett blanksteg. Detta skiljer sig från frågeloggar som använder startIndex och endIndex egenskaper. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Batch syntax mall för avsikter utan entiteter

Använd följande mall för att starta kommandofilen utan entiteter:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Om du inte vill testa entiteter omfattar den `entities` egenskap och ange en tom matris `[]`.


## <a name="common-errors-importing-a-batch"></a>Vanliga fel som importerar en batch

Vanliga fel är: 

> * Fler än 1 000 yttranden
> * Ett uttryck JSON-objekt som inte har en egenskap för entiteter. Egenskapen kan vara tomma matriser.
> * Ord som är märkt i flera entiteter
> * Entiteten etikett börjar eller slutar på ett blanksteg.

## <a name="batch-test-state"></a>Batch testtillstånd

LUIS spårar tillståndet för varje datauppsättning senaste testet. Detta inkluderar storleken (antal yttranden i batchen) för senaste körning datum och senaste resultat (antal har förväntade yttranden).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Testresultat för batch

Testresultat för batch är ett punktdiagram som kallas en matris med fel. Det här diagrammet är en 4-vägs-jämförelse av yttranden i kommandofilen och förväntade syftet för den aktuella modellen och entiteter. 

Data som pekar på den **falskt positivt** och **falska negativa** avsnitt visar fel, som bör undersökas. Om alla datapunkter som finns på den **sanna positiva** och **SANT negativt** avsnitt, och sedan din app är perfekt för den här datauppsättningen.

![Fyra delar av diagram](./media/luis-concept-batch-test/chart-sections.png)

Det här diagrammet hjälper dig att hitta yttranden som beräknar LUIS felaktigt baserat på dess aktuella utbildning. Resultaten visas per region i diagrammet. Välj enskilda datapunkter på grafen för att granska informationen om uttryck eller välj namnet på georegionen och granska resultatet från uttryck i den regionen.

![Batch-testning](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Fel i resultaten

Fel i batch-testet visar avsikter som inte är förväntad som anges i kommandofilen. Fel indikeras i de två red avsnitten i diagrammet. 

Avsnittet falskt positivt anger att ett uttryck matchade en avsikt eller enhet när den inte får ha. Det falska negativt anger ett uttryck inte matchade en avsikt eller enhet när den ska ha. 

## <a name="fixing-batch-errors"></a>Åtgärda fel

Om det uppstår fel i testet med batch kan du kan antingen lägga till flera uttryck i ett intent och/eller märka mer yttranden med entiteten för att göra diskriminering avsikter LUIS. Om du har lagt till yttranden och märkta dem och fortfarande få förutsägelse fel i testet med batch kan du lägga till en [frasen lista](luis-concept-feature.md) funktionen med domänspecifika ordförråd för att LUIS Lär dig snabbare. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [testa en batch](luis-how-to-batch-test.md)
