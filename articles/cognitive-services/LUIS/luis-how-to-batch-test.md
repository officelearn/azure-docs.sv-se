---
title: Så här utför du ett batchtest - LUIS
titleSuffix: Azure Cognitive Services
description: Använd batchtestuppsättningar (Language Understanding) för att hitta yttranden med felaktiga avsikter och entiteter.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: bfef7eae7158a05b09a3534e8fb44335333d8cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904345"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batchtestning med en uppsättning exempelyttranden

 Batchtestning är ett omfattande test på din nuvarande tränade modell för att mäta dess prestanda i LUIS. De datauppsättningar som används för batchtestning bör inte innehålla exempelyttranden i avsikter eller yttranden som tas emot från slutpunkten för förutsägelsekörning. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Importera en datauppsättningsfil för batchtestning

1. Välj **Testa** i det övre fältet och välj sedan **Batch-testpanel**.

    ![Länk för batchtestning](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Välj **Importera datauppsättning**. Dialogrutan **Importera ny datauppsättning** visas. Välj **Välj fil** och leta upp en JSON-fil med rätt [JSON-format](luis-concept-batch-test.md#batch-file-format) som inte innehåller *mer än 1 000* yttranden att testa.

    Importfel rapporteras i ett rött meddelandefält högst upp i webbläsaren. När en import har fel skapas ingen datauppsättning. Mer information finns i [Vanliga fel](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. Ange ett namn på datauppsättningsfilen i fältet **Datauppsättningsnamn.** Datauppsättningsfilen innehåller en **matris med yttranden,** inklusive *den märkta avsikten* och *entiteterna*. Granska [exempelbatchfilen](luis-concept-batch-test.md#batch-file-format) för syntax. 

4. Välj **Done** (Klar). Datauppsättningsfilen läggs till.

## <a name="run-rename-export-or-delete-dataset"></a>Köra, byta namn på, exportera eller ta bort datauppsättning

Om du vill köra, byta namn på, exportera eller ta bort datauppsättningen använder du ellipsknappen (***...***) i slutet av datauppsättningsraden.

![Åtgärder för datauppsättning](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Kör ett batchtest på din tränade app

Om du vill köra testet markerar du datauppsättningsnamnet. När testet är klart visar den här raden testresultatet för datauppsättningen.

![Batch testresultat](./media/luis-how-to-batch-test/run-test.png)

Datauppsättningen som kan hämtas är samma fil som laddades upp för batchtestning.

|Status|Betydelse|
|--|--|
|![Ikon för lyckad testgrön cirkel](./media/luis-how-to-batch-test/batch-test-result-green.png)|Alla yttranden lyckas.|
|![Misslyckad test röd x-ikon](./media/luis-how-to-batch-test/batch-test-result-red.png)|Minst en uttrycksavsikt matchade inte förutsägelsen.|
|![Redo att testa ikon](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Testet är klart att köras.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Visa batchtestresultat 

Om du vill granska batchtestresultaten väljer du **Se resultat**.

![Batch testresultat](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Filtrera diagramresultat

Om du vill filtrera diagrammet efter en viss avsikt eller entitet markerar du avsikten eller entiteten på filtreringspanelen på höger sida. Datapunkterna och deras distribution uppdateras i diagrammet enligt ditt val. 
 
![Visualiserat batchtestresultat](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Visa uttrycksdata med en punkt

I diagrammet håller du muspekaren över en datapunkt för att se vissheten i förutsägelsen. Välj en datapunkt för att hämta motsvarande uttryck i uttryckslistan längst ned på sidan. 

![Markerad uttryck](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Visa avsnittsdata

Markera avsnittsnamnet i diagrammet med fyra avsnitt, till exempel **Falskt positivt** längst upp till höger i diagrammet. Under diagrammet visas alla yttranden i det avsnittet under diagrammet i en lista. 

![Markerade yttranden efter avsnitt](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

I den här föregående bilden `switch on` är uttrycket märkt med TurnAllOn-avsikten, men får förutsägelsen av Ingen avsikt. Detta är en indikation på att TurnAllOn-avsikten behöver fler exempelyttranden för att göra den förväntade förutsägelsen. 

De två avsnitten i diagrammet i rött anger yttranden som inte matchade den förväntade förutsägelsen. Dessa anger yttranden som LUIS behöver mer utbildning. 

De två delarna av diagrammet i grönt matchade den förväntade förutsägelsen.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Nästa steg

Om testningen visar att LUIS-appen inte känner igen de rätta avsikter och entiteterna kan du arbeta för att förbättra LUIS-appens prestanda genom att märka fler yttranden eller lägga till funktioner. 

* [Etikett föreslagna yttranden med LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Använda funktioner för att förbättra LUIS-appens prestanda](luis-how-to-add-features.md) 
* [Förstå batchtestning med den här självstudien](luis-tutorial-batch-testing.md)
* [Lär dig batchtestkoncept](luis-concept-batch-test.md).
