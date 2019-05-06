---
title: Kontextuella data med roller - Språkförståelse
titleSuffix: Azure Cognitive Services
description: Hitta relaterade data baserat på kontext. Till exempel är ett ursprung och målplatser för en fysisk flytt från en byggnad och ett kontor till en annan byggnad och ett annat kontor relaterade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: diberry
ms.openlocfilehash: 2e2b2c39cf9039c78a610e9b28065b972484123e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069274"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Självstudier: Extrahera sammanhangsmässigt relaterade data från ett yttrande

I den här självstudien hittar du relaterade datadelar baserat på kontext. Exempel kan vara en ursprungsplats och målplatser för en transport från en stad till en annan. Båda datadelarna kan krävas, och de är relaterade till varandra.  

En roll kan användas med alla typer av fördefinierade eller anpassade entitet och används i både exempel yttranden och mönster. 

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa ny app
> * Lägga till avsikt 
> * Hämta information om ursprung och mål med hjälp av roller
> * Träna
> * Publicera
> * Hämta avsikter och entiteten roller från slutpunkten

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Relaterade data

Den här appen bestämmer var en medarbetare ska flyttas från ursprungsstaden till målstaden. Den använder en GeographyV2 fördefinierade entitet för att identifiera stadsnamn och den använder roller för att fastställa platstyper (original och beskrivning) inom uttryck.

En roll ska användas när entitetsdata att extrahera:

* Är relaterade till varandra i samband med uttryck.
* Använder specifika ordet om alternativ för att ange varje roll. Exempel på sådana ord: från/till, lämnar/ska till, bort från/till.
* Båda rollerna är ofta i samma uttryck, vilket gör att LUIS för att lära sig från frekventa sammanhangsberoende användningen.
* Båda måste grupperas och bearbetas av klientappen som en informationsenhet.

## <a name="create-a-new-app"></a>Skapa en ny app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Skapa en avsikt att flytta medarbetare mellan städer

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Välj **Create new intent** (Skapa ny avsikt). 

1. Ange `MoveEmployeeToCity` i popup-dialogrutan och välj sedan **Done** (Klar). 

    ![Skärmbild på dialogrutan Create new intent (Skapa ny avsikt)](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |Flytta John W. Smith lämnar Seattle på väg till Orlando|
    |överför Jill Jones från Seattle till Kairo|
    |Placera John Jackson bort från Tampa, ankommer till Atlanta |
    |Flytta Debra Doughtery till Tulsa från Chicago|
    |mv Jill Jones lämnar Kairo och är på väg till Tampa|
    |byt plats för Alice Anderson till Oakland från Redmond|
    |Carl Chamerlin från San Francisco till Redmond|
    |överför Steve Standish från San Diego mot Bellevue |
    |plocka bort Tanner Thompson från Kansas City och flytta över till Chicago|

    [![Skärmbild på LUIS-appen med nya talindata i avsikten MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Lägg till fördefinierade entitet geographyV2

Entiteten fördefinierade geographyV2, extraherar platsinformation, inklusive stadsnamn. Eftersom talade har två stadsnamn, som är relaterade till varandra i sammanhanget kan du använda roller för att extrahera den kontexten.

1. Välj **entiteter** från navigeringen till vänster.

1. Välj **Lägg till fördefinierade entitet**och välj sedan `geo` i sökfältet för att filtrera förskapade entiteter. 

    ![Lägg till geographyV2 fördefinierade entitet till appen](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Markera kryssrutan och välj **klar**.
1. I den **entiteter** väljer den **geographyV2** att öppna den nya entiteten. 
1. Lägg till två roller `Origin`, och `Destination`. 

    ![Lägga till roller i fördefinierade entitet](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Välj **avsikter** från vänster navigering, Välj den **MoveEmployeeToCity** avsikt. Observera stadsnamn är märkta med entitet som är färdiga **geogrpahyV2**.
1. I den första uttryck i listan väljer du ursprungsplatsen. En nedrullningsbar meny. Välj **geographyV2** i listan och följ sedan menyn över för att välja **ursprung**.

    [![Skärmbild av Markera ort som ursprungsplatsen](media/tutorial-entity-roles/tag-origin-city-with-role.png "Skärmbild av Markera ort som ursprungsplatsen")](media/tutorial-entity-roles/tag-origin-city-with-role.png#lightbox)

1. Använd metoden i föregående steg för att markera alla roller för platser i alla yttranden. 

    [![Skärmbild av platsentitet etiketterad i yttranden](media/tutorial-entity-roles/all-locations-marked-with-roles.png "Skärmbild av platsentitet etiketterad i yttranden")](media/tutorial-entity-roles/all-locations-marked-with-roles.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Lägga till exempelyttranden i avsikten Ingen 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att avsiktsändringarna kan testas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att frågor kan köras mot den tränade modellen från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Gå till slutet av webbadressen i adressfältet och ange `Please move Carl Chamerlin from Tampa to Portland`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Den här uttryck är inte samma som helst av taggade talade så det är ett bra test och ska returnera den `MoveEmployee` avsikt med den entitet som extraheras.

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
          "entity": "geographyV2",
          "role": "Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "geographyV2",
          "role": "Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    Rätt avsikten förväntas och entiteter matrisen har både ursprung och mål roller i motsvarande **entiteter** egenskapen.
    
## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Koncept för entiteter](luis-concept-entity-types.md)
* [Koncept för roller](luis-concept-roles.md)
* [Lista med fördefinierade entiteter](luis-reference-prebuilt-entities.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)
* [roles](luis-concept-roles.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapades en ny avsikt, och exempelyttranden lades till för sammanhangsmässigt inlärda data för ursprungs- och målplatser. När appen har tränats och publicerats kan ett klientprogram använda den informationen för att skapa en flyttningsbiljett med relevant information.

> [!div class="nextstepaction"] 
> [Lär dig hur du lägger till en sammansatt entitet](luis-tutorial-composite-entity.md) 
