---
title: Batch-testning - LUIS
titleSuffix: Azure Cognitive Services
description: Använd batchtestning för att kontinuerligt arbeta med ditt program för att förfina det och förbättra dess språkförståelse.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220054"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Batchtestning med 1 000 yttranden i LUIS-portalen

Batchtestning validerar din aktiva tränade version för att mäta dess förutsägelsenoggrannhet. Ett batchtest hjälper dig att visa riktigheten för varje avsikt och entitet i din aktiva version, vilket visar resultat med ett diagram. Granska batchtestresultaten för att vidta lämpliga åtgärder för att förbättra noggrannheten, till exempel lägga till fler exempelyttranden i en avsikt om appen ofta inte identifierar rätt avsikt eller märkningsentiteter i uttryck.

## <a name="group-data-for-batch-test"></a>Gruppdata för batchtest

Det är viktigt att yttranden som används för batchtestning är nya för LUIS. Om du har en datauppsättning yttranden delar du upp yttrandena i tre uppsättningar: exempelyttranden som lagts till i en avsikt, yttranden som tagits emot från den publicerade slutpunkten och yttranden som används för att batchtesta LUIS när det har tränats. 

## <a name="a-data-set-of-utterances"></a>En datauppsättning med yttranden

Skicka en kommandofil med yttranden, så kallade *datauppsättningar,* för batchtestning. Datauppsättningen är en JSON-formaterad fil som innehåller högst 1 000 etiketterade **icke-duplicerade** yttranden. Du kan testa upp till 10 datauppsättningar i en app. Om du behöver testa mer tar du bort en datauppsättning och lägger sedan till en ny.

|**Regler**|
|--|
|*Inga dubbla yttranden|
|1000 yttranden eller mindre|

*Dubbletter betraktas som exakta strängmatchningar, inte matchningar som tokeniseras först. 

## <a name="entities-allowed-in-batch-tests"></a>Entiteter som tillåts i batchtester

Alla anpassade entiteter i modellen visas i filtret för batchtestentiteter, även om det inte finns några motsvarande entiteter i batchfilsdata.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Filformat för batch

Kommandofilen består av yttranden. Varje uttryck måste ha en förväntad avsikt förutsägelse tillsammans med alla [maskininlärda entiteter](luis-concept-entity-types.md#types-of-entities) som du förväntar dig att identifieras. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Kommandosyntaxmall för avsikter med entiteter

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

Batchfilen använder egenskaperna **startPos** och **endPos** för att notera början och slutet av en entitet. Värdena är nollbaserade och bör inte börja eller sluta på ett blanksteg. Detta skiljer sig från frågeloggarna, som använder egenskaperna startIndex och endIndex. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Kommandosyntaxmall för avsikter utan entiteter

Använd följande mall för att starta batchfilen utan entiteter:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Om du inte vill testa entiteter tar du med egenskapen `entities` och anger värdet som en tom `[]`matris.


## <a name="common-errors-importing-a-batch"></a>Vanliga fel som importerar en batch

Vanliga fel är: 

> * Mer än 1 000 yttranden
> * Ett uttrycksobjekt för JSON som inte har en entitetsegenskap. Egenskapen kan vara en tom matris.
> * Word(s) märkta i flera entiteter
> * Entitetsetikett som börjar eller slutar på ett blanksteg.

## <a name="batch-test-state"></a>Testtillstånd för batch

LUIS spårar tillståndet för varje datauppsättnings senaste test. Detta inkluderar storleken (antalet yttranden i batchen), senaste körningsdatum och senaste resultat (antal framgångsrikt förutspådde yttranden).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Batch testresultat

Batchtestresultatet är ett punktdiagram, så kallat felmatris. Det här diagrammet är en 4-vägs jämförelse av yttranden i kommandofilen och den aktuella modellens förväntade avsikt och entiteter. 

Datapunkter i avsnitten **Falskt positivt** och **falskt negativt** anger fel som bör undersökas. Om alla datapunkter finns i avsnitten **Sant positivt** och **sant negativt** är appens noggrannhet perfekt på den här datauppsättningen.

![Fyra avsnitt i diagrammet](./media/luis-concept-batch-test/chart-sections.png)

Det här diagrammet hjälper dig att hitta yttranden som LUIS förutsäger felaktigt baserat på dess aktuella utbildning. Resultaten visas per region i diagrammet. Välj enskilda punkter i diagrammet om du vill granska uttrycksinformationen eller välj regionnamn för att granska uttrycksresultat i det området.

![Batch-testning](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Fel i resultaten

Fel i batchtestet anger avsikter som inte förutses som anges i batchfilen. Fel anges i de två röda delarna av diagrammet. 

Det falska positiva avsnittet anger att ett uttryck matchade en avsikt eller entitet när den inte borde ha gjort det. Det falska negativa anger att ett uttryck inte matchade en avsikt eller entitet när den borde ha gjort det. 

## <a name="fixing-batch-errors"></a>Åtgärda batchfel

Om det finns fel i batchtestningen kan du antingen lägga till fler yttranden i en avsikt och/eller märka fler yttranden med entiteten för att hjälpa LUIS att göra diskriminering mellan avsikter. Om du har lagt till yttranden och märkt dem och fortfarande får förutsägelsefel i batchtestning kan du överväga att lägga till en [fraslistefunktion](luis-concept-feature.md) med domänspecifik vokabulär som hjälper LUIS att lära sig snabbare. 

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [testar en batch](luis-how-to-batch-test.md)
