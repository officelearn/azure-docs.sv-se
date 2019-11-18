---
title: Etikett för entitets exempel uttryck
titleSuffix: Azure Cognitive Services
description: Lär dig hur du etiketterar en enhets medveten entitet med del komponenter i ett exempel på en uttryck på LUIS-portalen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135112"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Märk enhets enheten i ett exempel uttryck

Att märka en entitet i ett exempel uttryck visar LUIS har ett exempel på entiteten och var entiteten kan visas i uttryck. 

## <a name="labeling-machine-learned-entity"></a>Etikettering av datorn – lärt sig entiteten

Överväg frasen `hi, please I want a cheese pizza in 20 minutes`. 

1. Välj den vänstra texten och välj sedan den högra texten i entiteten. Den _fullständiga ordningen_ är märkt i följande bild.

    > [!div class="mx-imgBorder"]
    > ![etiketten har slutförts efter att enheten](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Välj entiteten i popup-fönstret. Etiketten fullständig pizza order-entitet innehåller alla ord (från vänster till höger på engelska) som är märkta. 

> [!TIP]
> De entiteter som är tillgängliga i popup-fönstret är i förhållande till kontexten där texten visas. Om du till exempel har en enhet på 5-nivå som har registrerats av enheten och du väljer text på den tredje nivån (anges med ett namngett entitetsnamn under exemplet uttryck), är entiteterna som är tillgängliga i popup-fönstret begränsade till kontexten för del komponenter i den tredje nivå (del komponenter på fjärde nivån). 

## <a name="review-labeled-text"></a>Granska etiketterad text

Granska exemplet på uttryck efter etikettering. LUIS använder den aktuella modellen i exemplet uttryck efter etikettering. Den heldragna linjen anger att texten har märkts. 

> [!div class="mx-imgBorder"]
> ![märkta den fullständiga enheten](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>När du ska träna

Om den aktuella modellen ska ha stöd för din märkta entitet, men exemplet uttryck fortsätter att visa texten som förväntat men inte märkt, träna din app.  

## <a name="confirm-predicted-entity"></a>Bekräfta förväntad entitet

Om den visuella indikatorn är över uttryck, anger den att texten är förväntad men _inte märkt än_. Om du vill omvandla förutsägelsen till en etikett väljer du uttryck och väljer **Bekräfta enhets förutsägelser**.

> [!div class="mx-imgBorder"]
> ![förutse att enheten har lärts](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>Namnge del komponent entitet genom att måla med en färgpalett

1. För att korrigera förutsägelser (entiteter, som visas ovanför exemplet uttryck), öppnar du entiteten entitet. 

    > [!div class="mx-imgBorder"]
    > ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. Välj del komponenten entitet. Den här åtgärden visas visuellt med en ny markör. Markören följer musen när du flyttar i portalen. 

    > [!div class="mx-imgBorder"]
    > ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. I uttryck-exemplet _målar_ du entiteten med markören. 

    > [!div class="mx-imgBorder"]
    > ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>Namnge matchning – text enheter till en enhet som har lärts ur enheten

Matchande textentiteter är fördefinierade entiteter, reguljära uttrycks enheter och list enheter. Du lägger till dem till en enhet som har registrerats av enheten, som begränsningar till en del komponent, när du skapar eller redigerar den enhet som har registrerats av enheten. 

**När de här begränsningarna har lagts till behöver du inte märka den matchande texten i exemplet uttryck.**

## <a name="entity-prediction-errors"></a>Enhets förutsägelse fel

Fel i enhets förutsägelse visar en varnings indikator. Detta anger att den förväntade entiteten inte matchar den märkta entiteten. 

> [!div class="mx-imgBorder"]
> ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Nästa steg

Använd [instrument panelen](luis-how-to-use-dashboard.md) och [Granska slut punkts yttranden](luis-how-to-review-endpoint-utterances.md) för att förbättra förutsägelse kvaliteten för din app.
