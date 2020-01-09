---
title: 'Självstudie: kontextuella data med roller – LUIS'
titleSuffix: Azure Cognitive Services
description: Hitta relaterade data baserat på kontext. Till exempel är ett ursprung och målplatser för en fysisk flytt från en byggnad och ett kontor till en annan byggnad och ett annat kontor relaterade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: cd646ef061a0be06a9b1a56b72a4f35d9796aa63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447895"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Självstudie: extrahera sammanhangsbaserade relaterade data från en uttryck

I den här självstudien hittar du relaterade datadelar baserat på kontext. Exempel kan vara en ursprungsplats och målplatser för en transport från en stad till en annan. Båda datadelarna kan krävas, och de är relaterade till varandra.

En roll kan användas med en fördefinierad eller anpassad entitetstyp och används i båda exemplen yttranden och Patterns.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa ny app
> * Lägga till avsikt
> * Hämta information om ursprung och mål med hjälp av roller
> * Lär in
> * Publicera
> * Hämta intentor och entitets roller från slut punkten

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Relaterade data

Den här appen bestämmer var en medarbetare ska flyttas från ursprungsstaden till målstaden. Den använder en GeographyV2-fördefinierad entitet för att identifiera Orts namnen och använder roller för att fastställa plats typerna (ursprung och mål) i uttryck.

En roll ska användas när enhets data ska extraheras:

* Är relaterad till varandra i kontexten för uttryck.
* Använder ett särskilt Word-val för att ange varje roll. Exempel på sådana ord: från/till, lämnar/ska till, bort från/till.
* Båda rollerna är ofta i samma uttryck, vilket gör det möjligt för LUIS att lära sig från denna frekventa kontext användning.
* Båda måste grupperas och bearbetas av klientappen som en informationsenhet.

## <a name="create-a-new-app"></a>Skapa en ny app

1. Logga in på LUIS-portalen för förhands granskning med URL: en för [https://preview.luis.ai](https://preview.luis.ai).

1. Välj **Skapa ny app**, ange namnet `HumanResources` och behåll standard kulturen, **engelska**. Lämna beskrivningen tom.

1. Välj **Done** (Klar).

## <a name="create-an-intent-to-move-employees-between-cities"></a>Skapa en avsikt att flytta medarbetare mellan städer

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Välj **Create new intent** (Skapa ny avsikt).

1. Ange `MoveEmployeeToCity` i popup-dialogrutan och välj sedan **Done** (Klar).

    > [!div class="mx-imgBorder"]
    > ![skärm bild av dialog rutan skapa ny avsikt med](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |flytta John o. Svensson att lämna Seattle till Orlando|
    |överför Jill Jones från Seattle till Kairo|
    |Placera John Jackson bort från Tampa, ankommer till Atlanta |
    |flytta Debra-Doughtery till Tulsa från Chicago|
    |mv Jill Jones lämnar Kairo och är på väg till Tampa|
    |byt plats för Alice Anderson till Oakland från Redmond|
    |Carl Chamerlin från San Francisco till Redmond|
    |överför Steve Standish från San Diego mot Bellevue |
    |plocka bort Tanner Thompson från Kansas City och flytta över till Chicago|

    > [!div class="mx-imgBorder"]
    > ![skärm bild av LUIS med nya yttranden i MoveEmployee avsikts](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Lägg till fördefinierad entitet geographyV2

Den fördefinierade entiteten geographyV2, extraherar plats information, inklusive Orts namn. Eftersom yttranden har två Orts namn, relaterade till varandra i sammanhanget, använder du roller för att extrahera kontexten.

1. Välj **entiteter** från den vänstra navigeringen.

1. Välj **Lägg till fördefinierad entitet**och välj sedan `geo` i Sök fältet för att filtrera de förinställda entiteterna.

    > [!div class="mx-imgBorder"]
    > ![lägga till geographyV2-fördefinierad entitet i appen](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Markera kryss rutan och välj **färdig**.
1. I listan **entiteter** väljer du **geographyV2** för att öppna den nya entiteten.
1. Lägg till två roller, `Origin`och `Destination`.

    > [!div class="mx-imgBorder"]
    > ![lägga till roller till en fördefinierad entitet](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

1. Välj **avsikter** från navigeringen till vänster och välj sedan **MoveEmployeeToCity** avsikt. Lägg märke till att Orts namnen är märkta med den fördefinierade entiteten **geographyV2**.
1. I verktygsfältet kontext väljer du Entity- **paletten**.

    > [!div class="mx-imgBorder"]
    > ![Välj Entity-paletten från verktygsfältet innehåll](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Välj den fördefinierade entiteten **geographyV2**och välj sedan **enhets kontrollen**.
1. I **entitets-kontrollen**väljer du en roll, **mål**. Detta ändrar mus markören. Använd markören för att märka texten i alla yttranden som är mål platsen.

    > [!div class="mx-imgBorder"]
    > ![Välj roll i paletten entitet](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Gå tillbaka till **entitetens kontrollant**, ändra till rollen som **ursprung**. Använd markören för att märka texten i alla yttranden som är ursprungs platsen.

## <a name="add-example-utterances-to-the-none-intent"></a>Lägga till exempelyttranden i avsikten Ingen

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att avsiktsändringarna kan testas

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att frågor kan köras mot den tränade modellen från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Gå till slutet av webbadressen i adressfältet och ange `Please move Carl Chamerlin from Tampa to Portland`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Den här uttryck är inte samma som någon av de märkta yttranden, så det är ett lyckat test och ska returnera `MoveEmployee` avsikten med den extraherade enheten.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```

    Rätt avsikt är förväntad och entiteter-matrisen har både ursprungs-och mål rollerna i motsvarande **entitets** -egenskap.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Entiteter-koncept](luis-concept-entity-types.md)
* [Roll koncept](luis-concept-roles.md)
* [Lista med färdiga entiteter](luis-reference-prebuilt-entities.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)
* [roles](luis-concept-roles.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapades en ny avsikt, och exempelyttranden lades till för sammanhangsmässigt inlärda data för ursprungs- och målplatser. När appen har tränats och publicerats kan ett klientprogram använda den informationen för att skapa en flyttningsbiljett med relevant information.

> [!div class="nextstepaction"]
> [Lär dig hur du lägger till en sammansatt entitet](luis-tutorial-composite-entity.md)
