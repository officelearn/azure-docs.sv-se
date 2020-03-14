---
title: Batch-testning – LUIS
titleSuffix: Azure Cognitive Services
description: Använda batch testning för att arbeta kontinuerligt för ditt program för att förfina och förbättra dess språkförståelse.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220054"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Batch-testning med 1000 yttranden i LUIS-portalen

Batch-testning verifierar din aktiva utbildade version för att mäta dess förutsägelse noggrannhet. Ett batch-test hjälper dig att Visa precisionen för varje avsikt och entitet i din aktiva version, vilket visar resultat med ett diagram. Granska resultaten av batch-testet för att vidta lämpliga åtgärder för att förbättra noggrannheten, till exempel lägga till fler exempel yttranden till en avsikt om din app ofta inte kan identifiera rätt avsikts-eller märknings enheter i uttryck.

## <a name="group-data-for-batch-test"></a>Gruppera data för batch-test

Det är viktigt att yttranden som används för att testa batch har LUIS. Om du har en data uppsättning med yttranden kan du dela upp yttranden i tre mängder: exempel på yttranden som har lagts till i avsikt, yttranden som tagits emot från den publicerade slut punkten och yttranden som används för batch-test LUIS efter att det har tränats. 

## <a name="a-data-set-of-utterances"></a>En data uppsättning yttranden

Skicka en batch-fil med yttranden, som kallas för en *data uppsättning*, för batch-testning. Data uppsättningen är en JSON-formaterad fil som innehåller högst 1 000 märkt **icke-duplicerade** yttranden. Du kan testa upp till 10 data uppsättningar i en app. Om du behöver testa mer tar du bort en data uppsättning och lägger till en ny.

|**Uttryck**|
|--|
|\* Inga dubbla yttranden|
|1000 yttranden eller mindre|

\* Dubbletter anses exakta strängen matchar, inte matchningar som är tokeniserad först. 

## <a name="entities-allowed-in-batch-tests"></a>Entiteter som tillåts i batch-tester

Alla anpassade entiteter i modellen visas i filtret batch test entiteter även om det finns inga motsvarande entiteter i batch-fildata.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Batch-filformat

Kommandofilen består av yttranden. Varje uttryck måste ha en förväntad förutsägelse för matchning tillsammans med alla [enheter](luis-concept-entity-types.md#types-of-entities) som du förväntar dig att identifiera. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Mall för satsvis syntax för avsikter med entiteter

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

Kommando filen använder egenskaperna **startPos** och **endPos** för att notera början och slutet på en entitet. Värdena får är nollbaserat och inte börja eller sluta på ett blanksteg. Detta skiljer sig från frågeloggar som använder startIndex och endIndex egenskaper. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Mall för satsvis syntax för avsikter utan entiteter

Använd följande mall för att starta kommando filen utan entiteter:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Om du inte vill testa entiteter tar du med egenskapen `entities` och anger värdet som en tom matris `[]`.


## <a name="common-errors-importing-a-batch"></a>Vanliga fel som importerar en batch

Vanliga fel är: 

> * Fler än 1 000 yttranden
> * Ett uttryck JSON-objekt som inte har en egenskap för entiteter. Egenskapen kan vara tomma matriser.
> * Ord som är märkt i flera entiteter
> * Entiteten etikett börjar eller slutar på ett blanksteg.

## <a name="batch-test-state"></a>Batch testtillstånd

LUIS spårar statusen för varje data uppsättnings senaste test. Detta inkluderar storleken (antal yttranden i batchen) för senaste körning datum och senaste resultat (antal har förväntade yttranden).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Testresultat för batch

Testresultat för batch är ett punktdiagram som kallas en matris med fel. Det här diagrammet är en 4-vägs-jämförelse av yttranden i kommandofilen och förväntade syftet för den aktuella modellen och entiteter. 

Data punkter i avsnitten **falskt positivt** och **falskt negativ** indikerar fel som bör undersökas. Om alla data punkter finns på det **sanna positiva** och **sanna negativa** avsnittet är appens noggrannhet perfekt i den här data uppsättningen.

![Fyra delar av diagram](./media/luis-concept-batch-test/chart-sections.png)

Det här diagrammet hjälper dig att hitta yttranden som beräknar LUIS felaktigt baserat på dess aktuella utbildning. Resultaten visas per region i diagrammet. Välj enskilda datapunkter på grafen för att granska informationen om uttryck eller välj namnet på georegionen och granska resultatet från uttryck i den regionen.

![Batch-testning](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Fel i resultaten

Fel i batch-testet visar avsikter som inte är förväntad som anges i kommandofilen. Fel indikeras i de två red avsnitten i diagrammet. 

Avsnittet falskt positivt anger att ett uttryck matchade en avsikt eller enhet när den inte får ha. Det falska negativt anger ett uttryck inte matchade en avsikt eller enhet när den ska ha. 

## <a name="fixing-batch-errors"></a>Åtgärda fel

Om det uppstår fel i testet med batch kan du kan antingen lägga till flera uttryck i ett intent och/eller märka mer yttranden med entiteten för att göra diskriminering avsikter LUIS. Om du har lagt till yttranden och märkt dem och fortfarande får förutsägelse fel i batch-testning, bör du överväga att lägga till en [fras lista](luis-concept-feature.md) med en domänbaserad vokabulär för att hjälpa Luis att lära dig snabbare. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [testar en batch](luis-how-to-batch-test.md)
