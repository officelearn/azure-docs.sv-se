---
title: Så här utför du en batch-test – LUIS
titleSuffix: Azure Cognitive Services
description: Använd Language Understanding (LUIS) batch test uppsättningar för att hitta yttranden med felaktiga intentor och entiteter.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "73904345"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batch-testning med en uppsättning exempel yttranden

 Batch-testning är ett omfattande test på din aktuella utbildade modell för att mäta prestanda i LUIS. De data uppsättningar som används för batch-testning bör inte innehålla exempel yttranden i de avsikter eller yttranden som tas emot från körnings slut punkten för förutsägelse. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Importera en data uppsättnings fil för batch-testning

1. Välj **test** i det översta fältet och välj sedan **batch test panel**.

    ![Batch-testning, länk](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Välj **Importera data uppsättning**. Dialog rutan **Importera ny data uppsättning** visas. Välj **Välj fil** och leta upp en JSON-fil med rätt [JSON-format](luis-concept-batch-test.md#batch-file-format) som inte innehåller *fler än 1 000* yttranden för att testa.

    Import fel rapporteras i ett rött meddelande fält överst i webbläsaren. När en import innehåller fel skapas ingen data uppsättning. Mer information finns i [vanliga fel](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. I fältet **data uppsättnings namn** anger du ett namn för din data uppsättnings fil. Data uppsättnings filen innehåller en **matris med yttranden** , inklusive *etikettens avsikt* och *entiteter*. Granska syntaxen i [exempel kommando filen](luis-concept-batch-test.md#batch-file-format) . 

4. Välj **Klar**. Data uppsättnings filen har lagts till.

## <a name="run-rename-export-or-delete-dataset"></a>Köra, byta namn på, exportera eller ta bort data uppsättning

Om du vill köra, byta namn på, exportera eller ta bort data uppsättningen använder du knappen med tre punkter (***...***) i slutet av data mängds raden.

![Data mängds åtgärder](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Köra ett batch-test i din utbildade app

Om du vill köra testet väljer du data uppsättningens namn. När testet är klart visar den här raden test resultatet för data uppsättningen.

![Batch test resultat](./media/luis-how-to-batch-test/run-test.png)

Den nedladdnings bara data uppsättningen är samma fil som överfördes för batch-testning.

|Status|Betydelse|
|--|--|
|![Lyckad test grön cirkel-ikon](./media/luis-how-to-batch-test/batch-test-result-green.png)|Alla yttranden har slutförts.|
|![Det gick inte att testa röd x-ikonen](./media/luis-how-to-batch-test/batch-test-result-red.png)|Minst en uttryck-avsikt matchade inte förutsägelsen.|
|![Indikator för redo att testa](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Testet är klart att köras.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Visa batch test resultat 

Om du vill granska resultaten för batch-testet väljer du **se resultat**.

![Batch test resultat](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Filtrera diagram resultat

Om du vill filtrera diagrammet efter ett särskilt syfte eller en entitet väljer du avsikten eller entiteten i filter panelen på höger sida. Data punkterna och deras distributions uppdatering i diagrammet efter ditt val. 
 
![Visuellt batch test resultat](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Visa uttryck-data med en punkt

Hovra över en data punkt i diagrammet för att se den aktuella uppskattningens resultat. Välj en data punkt för att hämta motsvarande uttryck i listan yttranden längst ned på sidan. 

![Valda uttryck](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Visa avsnitts data

I det fyra avsnitts diagrammet väljer du avsnittets namn, till exempel **falskt positivt** längst upp till höger i diagrammet. Under diagrammet visas alla yttranden i avsnittet nedanför diagrammet i en lista. 

![Valda yttranden efter avsnitt](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

I den här bilden är uttryck `switch on` märkt med TurnAllOn-avsikten men fick förutsägelsen ingen avsikt. Detta är en indikation på att TurnAllOn-avsikten kräver fler exempel yttranden för att göra den förväntade förutsägelsen. 

De två avsnitten i diagrammet i rött indikerar yttranden som inte matchar förväntade förutsägelser. Dessa indikerar yttranden som LUIS behöver mer utbildning. 

De två avsnitten i diagrammet i grönt matchade förväntade förutsägelse.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Nästa steg

Om testningen visar att LUIS-appen inte känner igen rätt avsikter och entiteter kan du arbeta för att förbättra LUIS-appens prestanda genom att märka fler yttranden eller lägga till funktioner. 

* [Märk föreslagna yttranden med LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Använd funktioner för att förbättra LUIS-appens prestanda](luis-how-to-add-features.md) 
* [Förstå batch-testning med den här självstudien](luis-tutorial-batch-testing.md)
* [Lär dig att testa koncept](luis-concept-batch-test.md).
