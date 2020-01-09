---
title: 'Självstudie: förutsäga-LUIS'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en anpassad app som förutspår en användares avsikt. Den här appen är den enklaste typen av LUIS-app eftersom den inte extraherar olika dataelement från yttranden, som e-postadresser eller datum.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 508fa192a014501bad6488e5be8278731230913b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381570"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Självstudie: Bygg en LUIS-app för att fastställa användar avsikter

I den här självstudien skapar du en anpassad app som förutsäger en användares avsikt baserat på uttryck (text).

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en ny app
> * Skapa avsikter
> * Lägga till exempelyttranden
> * Träna appen
> * Publicera app
> * Hämta förutsägelse för avsikt från slut punkt

## <a name="user-intentions-as-intents"></a>Användares syften som avsikter

Syftet med appen är att avgöra syftet med en konversationsbaserad text på naturligt språk:

`I'd like to order a veggie pizza with a salad on the side.`

Dessa syften är indelade i **avsikter**.

|Avsikt|Syfte|
|--|--|
|`ModifyOrder`|Fastställa användarens pizza-ordning.|
|`Greeting`|Starta bot-konversation.|
|`ConfirmOrder`|Bekräfta pizza-ordningen.|
|`None`|Ta reda på om användaren frågar något som appen inte ska svara på. Den här avsikten tillhandahålls som en del av att skapa appen och kan inte tas bort. |

## <a name="create-a-new-app"></a>Skapa en ny app

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Skapa en ny avsikt

1. I portalen, i appens **build** -avsnitt, väljer du **+ skapa**. Ange det nya namnet på avsikten `OrderPizza`och välj sedan **färdig**.

    `OrderPizza` avsikten förutsägs när: en användare vill beställa en pizza.

1. Lägg till flera exempelyttranden till den här avsikten som du förväntar dig att en användare kan fråga:

    |`OrderPizza` exempel yttranden|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|

    ![Lägga till exempelyttranden](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Genom att tillhandahålla _exempelyttranden_ tränar du LUIS i vilka typer av yttranden som ska förväntas i samband med den här avsikten.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Skapa återstående avsikter

1. Skapa `Greeting` avsikten och Lägg till följande exempel yttranden. Detta är avsikten att avgöra om en användare påbörjar en ny pizza order-konversation.

    |`Greeting` exempel yttranden|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Skapa `Confirm` avsikten och Lägg till följande exempel yttranden. Det här är avsikten att avgöra om en användare utför beställningar och godkänner beställnings detaljerna.

    |`Confirm` exempel yttranden|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Ingen avsikts exempel yttranden

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Träna appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publicera appen

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Hämta avsikts förutsägelse

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av webb adressen i adress fältet och ange:

    `get a medium vegetarian pizza for delivery`

    Detta är inte exakt detsamma som ett exempel på en uttryck, så det är ett bra test för att se om LUIS kan lära sig vad som ska förväntas i detta syfte.

    Den sista frågesträngsparametern är `query`, yttrande**frågan**. Det här yttrandet är inte identiskt med något av exempelyttrandena. Det är ett bra test och bör returnera avsikten `OrderPizza` som avsikten med högst poäng.

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    Entitets-matrisen är tom eftersom den här appen för närvarande inte har några entiteter (enhets data i uttryck som ska extraheras).

    I JSON-resultatet identifieras avsikten med högst poäng i egenskapen **`prediction.topIntent`** . Alla resultat är mellan 1 och 0, med bättre poäng som är närmare 1.

1. Ändra URL- **Frågeparametern** till riktad **hälsnings** avsikt:

    `Howdy`

    Detta är inte exakt detsamma som ett exempel på en uttryck, så det är ett bra test för att se om LUIS kan lära sig vad som ska förväntas i detta syfte.

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    Den här förutsägelseen har en säkerhets poäng på 44%. Om du vill öka förtroende poängen lägger du till mellan 15 och 30 exempel yttranden.

## <a name="client-application-next-steps"></a>Nästa steg för klientprogrammet

När LUIS returnerar JSON-svaret är LUIS färdig med förfrågningen. LUIS svarar inte på användarnas yttranden utan identifierar bara vilken typ av information som efterfrågas på det naturliga språket. Konversationsuppföljningen tillhandahålls av klientprogram, till exempel en Azure-robot.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Typer av entiteter](luis-concept-entity-types.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en LUIS-app, skapat avsikter, lagt till exempel yttranden för varje avsikt, lagt till exempel yttranden i ingen avsikt, utbildad, publicerad och testad vid slut punkten. Det här är de grundläggande stegen i att skapa en LUIS-modell.

> [!div class="nextstepaction"]
> [Lägg till en desammansättnings bar entitet i den här appen](tutorial-machine-learned-entity.md)
