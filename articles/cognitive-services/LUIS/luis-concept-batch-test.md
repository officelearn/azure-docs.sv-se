---
title: Batch testa LUIS-appen – Language Understanding
titleSuffix: Azure Cognitive Services
description: Använda batch testning för att arbeta kontinuerligt för ditt program för att förfina och förbättra dess språkförståelse.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/06/2018
ms.author: diberry
ms.openlocfilehash: 6c621d3cfc56b20511b16d014f7925cb92ccc279
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025871"
---
# <a name="batch-testing-in-luis"></a>Batch testa i LUIS

Batch testning validerar din [active](luis-concept-version.md#active-version) tränade modellen att mäta dess prognosens noggrannhet kan förbättras. Ett batch-test kan du visa det arbete du utfört varje avsikt och entiteten i din aktuella tränade modellen i ett diagram. Granska resultaten av batch för att vidta lämpliga åtgärder för att förbättra noggrannhet, som att lägga till fler exempel yttranden till en avsikt om din app ofta inte kan identifiera rätt avsikten.

## <a name="group-data-for-batch-test"></a>Gruppera data för batch-test
Det är viktigt att yttranden som används för att testa batch har LUIS. Om du har en datauppsättning för yttranden dela yttranden i tre grupper: yttranden som lagts till i en avsikt, yttranden som togs emot från den publicerade slutpunkten och yttranden som används för att testa batch LUIS när den har installerats. 

## <a name="a-dataset-of-utterances"></a>En datauppsättning för yttranden
Skicka en kommandofil av yttranden som kallas en *datauppsättning*, för att testa batch. Datauppsättningen är en JSON-formaterad fil som innehåller högst 1 000 märkta **icke dubblett** yttranden. Du kan testa upp till 10 datauppsättningar i en app. Om du vill testa mer, ta bort en datauppsättning och sedan lägga till en ny.

|**Regler**|
|--|
|* Inga dubbla yttranden|
|Inga underordnade hierarkisk entitet|
|1000 yttranden eller mindre|

* Dubbletter anses exakta strängen matchar, inte matchningar som är tokeniserad först. 

## <a name="entities-allowed-in-batch-tests"></a>Entiteter som tillåts i batch-tester
Entiteter är enkel, hierarkisk föräldrar och sammansatta. Alla entiteter av de här typerna visas i filtret batch test entiteter även om det finns inga motsvarande entiteter i batchfilen.


<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>Batch-filformat
Kommandofilen består av yttranden. Varje uttryck måste ha en förväntade avsikt förutsägelse tillsammans med eventuella [datorn lärt dig entiteter](luis-concept-entity-types.md#types-of-entities) du förväntar dig att identifieras. 

Följande är ett exempel på en batchfil med korrekt syntax:

```JSON
[
  {
    "text": "Are there any janitorial jobs currently open?",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 14,
            "endPos": 23
        }
    ]
  },
  {
    "text": "I would like a fullstack typescript programming with azure job",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 15,
            "endPos": 46
        }
    ]
  },
  {
    "text": "Is there a database position open in Los Colinas?",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 11,
            "endPos": 18
        }
    ]
  },
  {
    "text": "Please find database jobs open today in Seattle",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 12,
            "endPos": 19
        }
    ]
  }
]
```

## <a name="batch-syntax-template"></a>Mall för batch-syntax

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

Batchfilen använder den **startPos** och **endPos** egenskaper att notera i början och slutet av en entitet. Värdena får är nollbaserat och inte börja eller sluta på ett blanksteg. 

Detta skiljer sig från frågeloggar som använder startIndex och endIndex egenskaper. 


## <a name="common-errors-importing-a-batch"></a>Vanliga fel som importerar en batch
Vanliga fel är: 

> * Fler än 1 000 yttranden
> * Ett uttryck JSON-objekt som inte har en egenskap för entiteter
> * Ord som är märkt i flera entiteter

## <a name="batch-test-state"></a>Batch testtillstånd
LUIS spårar tillståndet för varje datauppsättning senaste testet. Detta inkluderar storleken (antal yttranden i batchen) för senaste körning datum och senaste resultat (antal har förväntade yttranden).

<a name="sections-of-the-results-chart"></a>
## <a name="batch-test-results"></a>Testresultat för batch
Testresultat för batch är ett punktdiagram som kallas en matris med fel. Det här diagrammet är en 4-vägs-jämförelse av yttranden i filen och den aktuella modellen förväntade avsikten och entiteter. 

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
