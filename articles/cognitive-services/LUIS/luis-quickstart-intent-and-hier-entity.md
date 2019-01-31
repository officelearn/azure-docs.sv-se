---
title: Hierarkisk entitet
titleSuffix: Azure Cognitive Services
description: Hitta relaterade datadelar baserat på kontext. Till exempel är ett ursprung och målplatser för en fysisk flytt från en byggnad och ett kontor till en annan byggnad och ett annat kontor relaterade.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: dc8b9831b89aade7ca5dfd3e1f53b9dccc15e66b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217041"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Självstudier: Extrahera sammanhangsmässigt relaterade data från ett yttrande

I den här självstudien hittar du relaterade datadelar baserat på kontext. Exempel kan vara en ursprungsplats och målplatser för en transport från en stad till en annan. Båda datadelarna kan krävas, och de är relaterade till varandra.  

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa ny app
> * Lägga till avsikt 
> * Lägg till platshierarkisk entitet med ursprung och underordnade destinationselement
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>Hierarkiska data

Den här appen bestämmer var en medarbetare ska flyttas från ursprungsstaden till målstaden. Den använder den hierarkiska entiteten för att urskilja platser i yttrandet. 

Den hierarkiska entiteten passar bra för den här typen av data eftersom följande gäller för de två datadelarna, de underordnade platserna:

* De är enkla entiteter.
* De är relaterade till varandra i yttrandet.
* Specifika ord används för att ange varje entitet. Exempel på sådana ord: från/till, lämnar/ska till, bort från/till.
* Båda underordnade element förekommer ofta i samma yttrande. 
* Båda måste grupperas och bearbetas av klientappen som en informationsenhet.

## <a name="create-a-new-app"></a>Skapa en ny app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Skapa en avsikt att flytta medarbetare mellan städer

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Välj **Create new intent** (Skapa ny avsikt). 

1. Ange `MoveEmployeeToCity` i popup-dialogrutan och välj sedan **Done** (Klar). 

    ![Skärmbild på dialogrutan Create new intent (Skapa ny avsikt)](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |flytta John W. Smith, som lämnar Seattle och är på väg till Dallas|
    |överför Jill Jones från Seattle till Kairo|
    |Placera John Jackson bort från Tampa, ankommer till Atlanta |
    |flytta Debra Doughtery till Tulsa från Dallas|
    |mv Jill Jones lämnar Kairo och är på väg till Tampa|
    |byt plats för Alice Anderson till Oakland från Redmond|
    |Carl Chamerlin från San Francisco till Redmond|
    |överför Steve Standish från San Diego mot Bellevue |
    |plocka bort Tanner Thompson från Kansas City och flytta över till Chicago|

    [ ![Skärmbild på LUIS-appen med nya yttranden i avsikten MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>Skapa en platsentitet
LUIS-appen behöver förstå vad en plats är genom att märka ursprungs- och målplatser i yttranden. Om du behöver se ett yttrande i tokenvyn (obearbetad) väljer du växlingsknappen i fältet ovanför yttrandena som är märkta **Entities View** (Entitetsvy). När du byter läge är kontrollen märkt **Tokens View** (Tokenvy).

Ta följande yttrande som exempel:

```json
move John W. Smith leaving Seattle headed to Dallas
```

I yttrandet finns två angivna platser: `Seattle` och `Dallas`. Båda grupperas som underordnade element i en hierarkisk enhet, `Location`, eftersom båda datadelar behöver extraheras från yttrandet för att begäran ska slutföras i klientprogrammet och de är relaterade till varandra. 
 
Om endast ett underordnat element (ursprung eller destination) för en hierarkisk entitet finns extraheras det ändå. Det är inte nödvändigt att hitta alla underordnade element för att endast ett eller några element ska kunna extraheras. 

1. I yttrandet `move John W. Smith leaving Seattle headed to Dallas` väljer du ordet `Seattle`. En listrutemeny visas med en textruta längst upp. Ange entitetsnamnet `Location` i textrutan och välj sedan **Create new entity** (Skapa ny entitet) i listrutan. 

    [![Skärmbild av skapande av ny entitet på avsiktssidan](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "Skärmbild av skapande av ny entitet på avsiktssidan")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. I popup-fönstret väljer du entitetstypen **Hierarchical** (Hierarkisk) med `Origin` och `Destination` som underordnade entiteter. Välj **Done** (Klar).

    ![Skärmbild av popup-fönstret för entitetsskapande för den nya platsentiteten](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Skärmbild av popup-fönstret för entitetsskapande för den nya platsentiteten")

1. Etiketten för `Seattle` har märkts som `Location` eftersom LUIS inte känner till om termen var ursprunget, målet, eller ingetdera. Välj `Seattle`, välj sedan **Location** (Plats), följ därefter menyn till höger och välj `Origin`.

    [![Skärmbild av popup-fönstret för entitetsetikettering vid ändring av platsentitets underordnad](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "Skärmbild av popup-fönstret för entitetsetikettering vid ändring av platsentitets underordnad")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. Märk de andra platserna i alla andra yttranden. När alla platser är märkta börjar yttrandena att få ett mönster. 

    [![Skärmbild av platsentitet etiketterad i yttranden](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "Skärmbild av platsentitet etiketterad i yttranden")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    Den röda understrykningen anger att LUIS inte är säker vad gäller entiteten. Träning löser detta. 

## <a name="add-example-utterances-to-the-none-intent"></a>Lägga till exempelyttranden i avsikten Ingen 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att avsiktsändringarna kan testas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att frågor kan köras mot den tränade modellen från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Gå till slutet av webbadressen i adressfältet och ange `Please move Carl Chamerlin from Tampa to Portland`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `MoveEmployee` med den hierarkiska entiteten extraherad.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    Den rätta avsikten förutsägs och entitetsmatrisen har både ursprungs- och målvärdena i motsvarande egenskap för **entiteter**.
    
## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* Konceptuell information om [hierarkisk entitet](luis-concept-entity-types.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)
* [Roller jämfört med hierarkiska entiteter](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [Förbättra förutsägelser med mönster](luis-concept-patterns.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapades en ny avsikt, och exempelyttranden lades till för sammanhangsmässigt inlärda data för ursprungs- och målplatser. När appen har tränats och publicerats kan ett klientprogram använda den informationen för att skapa en flyttningsbiljett med relevant information.

> [!div class="nextstepaction"] 
> [Lär dig hur du lägger till en sammansatt entitet](luis-tutorial-composite-entity.md) 
