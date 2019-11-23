---
title: 'Tutorial: Predict intentions - LUIS'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en anpassad app som förutspår en användares avsikt. Den här appen är den enklaste typen av LUIS-app eftersom den inte extraherar olika dataelement från yttranden, som e-postadresser eller datum.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 4d096ee829a425af3763c212daf5049acccf9f19
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325928"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Tutorial: Build a LUIS app to determine user intentions

In this tutorial, you create a custom app that predicts a user's intention based on the utterance (text). 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en ny app 
> * Skapa avsikter
> * Lägga till exempelyttranden
> * Träna appen
> * Publicera app
> * Get intent prediction from endpoint


[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]

## <a name="user-intentions-as-intents"></a>Användares syften som avsikter

Syftet med appen är att avgöra syftet med en konversationsbaserad text på naturligt språk: 

`I'd like to order a veggie pizza with a salad on the side.`

Dessa syften är indelade i **avsikter**. 

|Avsikt|Syfte|
|--|--|
|`ModifyOrder`|Determine user's pizza order.|
|`Greeting`|Begin bot conversation.|
|`ConfirmOrder`|Confirm pizza order.|
|`None`|Determine if user is asking something the app is not supposed to answer. Den här avsikten tillhandahålls som en del av att skapa appen och kan inte tas bort. |

## <a name="create-a-new-app"></a>Skapa en ny app

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Skapa en ny avsikt 

1. In the portal, inside the app's **Build** section, select **+ Create**. Enter the new intent name, `OrderPizza`, then select **Done**.

    The `OrderPizza` intent is predicted when: a user wants to order a pizza. 

1. Lägg till flera exempelyttranden till den här avsikten som du förväntar dig att en användare kan fråga:

    |`OrderPizza` example utterances|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|

    ![Lägga till exempelyttranden](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Genom att tillhandahålla _exempelyttranden_ tränar du LUIS i vilka typer av yttranden som ska förväntas i samband med den här avsikten. 

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]    

## <a name="create-remaining-intents"></a>Create remaining intents

1. Create the `Greeting` intent and add the following example utterances. This is the intent to determine if a user is beginning a new pizza order conversation.

    |`Greeting` example utterances|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Create the `Confirm` intent and add the following example utterances. This is the intent to determine if a user is done ordering and accepts the order details. 

    |`Confirm` example utterances|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>None intent example utterances

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Träna appen 

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publish the app 

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)] 

## <a name="get-intent-prediction"></a>Get intent prediction

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Go to the end of the URL in the address bar and enter:

    `get a medium vegetarian pizza for delivery` 

    This is not exactly the same as an example utterance so it is a good test to see if LUIS can learn what should be predicted with this intent.

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

    The entities array is empty because this app currently does not have any entities (unit of data inside the utterance to extract). 

    I JSON-resultatet identifieras avsikten med högst poäng i egenskapen **`prediction.topIntent`** . All scores are between 1 and 0, with the better score being closer to 1. 

1. Change the URL **query** parameter to target the **Greeting** intent:

    `Howdy`

    This is not exactly the same as an example utterance so it is a good test to see if LUIS can learn what should be predicted with this intent. 

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
 
    This prediction has a 44% confidence score. To increase the confidence score, add between 15 and 30 example utterances.  

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

This tutorial created a LUIS app, created intents, added example utterances to each intent, added example utterances to the None intent, trained, published, and tested at the endpoint. Det här är de grundläggande stegen i att skapa en LUIS-modell. 

> [!div class="nextstepaction"]
> [Add a decomposable entity to this app](tutorial-machine-learned-entity.md)
