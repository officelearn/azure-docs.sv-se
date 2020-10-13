---
title: Batch-testning – LUIS
titleSuffix: Azure Cognitive Services
description: Använd batch-testning för att kontinuerligt arbeta med ditt program för att förfina det och förbättra dess språk förståelse.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: f3a8f5ef8119d9895f67e07ea1b68c660be59f9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541874"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Batch-testning med 1000 yttranden i LUIS-portalen

Batch-testning verifierar din aktiva utbildade version för att mäta dess förutsägelse noggrannhet. Ett batch-test hjälper dig att Visa precisionen för varje avsikt och entitet i din aktiva version, vilket visar resultat med ett diagram. Granska resultaten av batch-testet för att vidta lämpliga åtgärder för att förbättra noggrannheten, till exempel lägga till fler exempel yttranden till en avsikt om din app ofta inte kan identifiera rätt avsikts-eller märknings enheter i uttryck.

## <a name="group-data-for-batch-test"></a>Gruppera data för batch-test

Det är viktigt att yttranden som används för batch-testning är nya för LUIS. Om du har en data uppsättning med yttranden kan du dela upp yttranden i tre mängder: exempel på yttranden som har lagts till i avsikt, yttranden som tagits emot från den publicerade slut punkten och yttranden som används för batch-test LUIS efter att det har tränats.

## <a name="a-data-set-of-utterances"></a>En data uppsättning yttranden

Skicka en batch-fil med yttranden, som kallas för en *data uppsättning*, för batch-testning. Data uppsättningen är en JSON-formaterad fil som innehåller högst 1 000 märkt **icke-duplicerade** yttranden. Du kan testa upp till 10 data uppsättningar i en app. Om du behöver testa mer tar du bort en data uppsättning och lägger till en ny.

|**Regler**|
|--|
|* Inga dubbletter av yttranden|
|1000 yttranden eller mindre|

* Dubbletter betraktas som exakta sträng matchningar, inte matchningar som är token först.

## <a name="entities-allowed-in-batch-tests"></a>Entiteter som tillåts i batch-test

Alla anpassade entiteter i modellen visas i filtret batch-test-entiteter även om det inte finns några motsvarande entiteter i batch-fildata.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Batch-filformat

Kommando filen består av yttranden. Varje uttryck måste ha en förväntad förutsägelse för matchning tillsammans med alla [Machine Learning-enheter](luis-concept-entity-types.md#types-of-entities) som du förväntar dig att identifiera.

## <a name="batch-syntax-template-for-intents-with-entities"></a>Mall för satsvis syntax för avsikter med entiteter

Använd följande mall för att starta kommando filen:

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

Kommando filen använder egenskaperna **startPos** och **endPos** för att notera början och slutet på en entitet. Värdena är noll-baserade och får inte börja eller sluta med ett blank steg. Det skiljer sig från frågans loggar, som använder start egenskaper och endIndex-egenskaper.

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

Om du inte vill testa entiteter, inkluderar du `entities` egenskapen och anger värdet som en tom matris `[]` .


## <a name="common-errors-importing-a-batch"></a>Vanliga fel vid import av en batch

Vanliga fel är:

> * Mer än 1 000 yttranden
> * Ett uttryck JSON-objekt som inte har någon entitets-egenskap. Egenskapen kan vara en tom matris.
> * Ord som är märkta i flera entiteter
> * Enhets etiketten startar eller slutar med ett blank steg.

## <a name="batch-test-state"></a>Batch-testläge

LUIS spårar statusen för varje data uppsättnings senaste test. Detta inkluderar storleken (antalet yttranden i batchen), senaste körnings datum och det senaste resultatet (antalet förväntade yttranden).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Batch test resultat

Batch-test resultatet är ett punkt diagram som kallas fel mat ris. Det här diagrammet är en 4-vägs jämförelse av yttranden i kommando filen och den aktuella modellens förväntade avsikt och entiteter.

Data punkter i avsnitten **falskt positivt** och **falskt negativ** indikerar fel som bör undersökas. Om alla data punkter finns på det **sanna positiva** och **sanna negativa** avsnittet är appens noggrannhet perfekt i den här data uppsättningen.

![Fyra delar av diagrammet](./media/luis-concept-batch-test/chart-sections.png)

Det här diagrammet hjälper dig att hitta yttranden som LUIS förutsäger felaktigt baserat på dess aktuella utbildning. Resultaten visas per region i diagrammet. Välj enskilda punkter i grafen för att granska uttryck-informationen eller Välj region namn för att granska uttryck resultat i den regionen.

![Batch-testning](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Fel i resultaten

Fel i batch-testet anger avsikter som inte förutsägs enligt vad som anges i kommando filen. Felen visas i de två röda avsnitten i diagrammet.

Avsnittet falsk positiv indikerar att en uttryck matchade ett avsikts-eller entitets när den inte borde ha det. Det falska negativa värdet indikerar att en uttryck inte matchade en avsikts-eller entitet när den borde ha.

## <a name="fixing-batch-errors"></a>Korrigera batch-fel

Om det finns fel i batch-testning kan du antingen lägga till fler yttranden till ett avsikts sätt och/eller lägga till etiketten mer yttranden med entiteten för att hjälpa LUIS att göra diskrimineringen mellan olika syften. Om du har lagt till yttranden och märkt dem och fortfarande får förutsägelse fel i batch-testning, bör du överväga att lägga till en [fras lista](luis-concept-feature.md) med en domänbaserad vokabulär för att hjälpa Luis att lära dig snabbare.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [testar en batch](luis-how-to-batch-test.md)
