---
title: 'Självstudiekurs: Kontextuella data med roller - LUIS'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75447895"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Självstudiekurs: Extrahera kontextuellt relaterade data från ett uttryck

I den här självstudien hittar du relaterade datadelar baserat på kontext. Exempel kan vara en ursprungsplats och målplatser för en transport från en stad till en annan. Båda datadelarna kan krävas, och de är relaterade till varandra.

En roll kan användas med valfri fördefinierad eller anpassad entitetstyp och användas i både exempelyttranden och mönster.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa ny app
> * Lägga till avsikt
> * Hämta ursprungs- och målinformation med hjälp av roller
> * Träna
> * Publicera
> * Hämta avsikter och entitetsroller från slutpunkten

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Relaterade data

Den här appen bestämmer var en medarbetare ska flyttas från ursprungsstaden till målstaden. Den använder en fördefinierad entitet för GeografiV2 för att identifiera ortnamnen och använder roller för att bestämma platstyper (ursprung och mål) inom uttrycket.

En roll bör användas när entitetsdata för att extrahera:

* Är relaterade till varandra i samband med yttrandet.
* Använder specifika ordval för att ange varje roll. Exempel på sådana ord: från/till, lämnar/ska till, bort från/till.
* Båda rollerna är ofta i samma uttryck, vilket gör att LUIS kan lära av den här frekventa kontextuella användningen.
* Båda måste grupperas och bearbetas av klientappen som en informationsenhet.

## <a name="create-a-new-app"></a>Skapa en ny app

1. Logga in på LUIS-portalen [https://preview.luis.ai](https://preview.luis.ai)med URL:en till .

1. Välj **Skapa ny app,** ange namnet `HumanResources` och behåll standardkulturen, **engelska**. Lämna beskrivningen tom.

1. Välj **Done** (Klar).

## <a name="create-an-intent-to-move-employees-between-cities"></a>Skapa en avsikt att flytta medarbetare mellan städer

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Välj **Create new intent** (Skapa ny avsikt).

1. Ange `MoveEmployeeToCity` i popup-dialogrutan och välj sedan **Done** (Klar).

    > [!div class="mx-imgBorder"]
    > ![Skärmbild på dialogrutan Create new intent (Skapa ny avsikt)](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |flytta John W. Smith lämnar Seattle på väg till Orlando|
    |överför Jill Jones från Seattle till Kairo|
    |Placera John Jackson bort från Tampa, ankommer till Atlanta |
    |flytta Debra Doughtery till Tulsa från Chicago|
    |mv Jill Jones lämnar Kairo och är på väg till Tampa|
    |byt plats för Alice Anderson till Oakland från Redmond|
    |Carl Chamerlin från San Francisco till Redmond|
    |överför Steve Standish från San Diego mot Bellevue |
    |plocka bort Tanner Thompson från Kansas City och flytta över till Chicago|

    > [!div class="mx-imgBorder"]
    > ![Skärmbild på LUIS-appen med nya talindata i avsikten MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Lägg till fördefinierad entitetsgeografiV2

Den fördefinierade entiteten, geographyV2, extraherar platsinformation, inklusive stadsnamn. Eftersom yttrandena har två ortnamn, som relaterar till varandra i sitt sammanhang, använder du roller för att extrahera det sammanhanget.

1. Välj **Entiteter** från navigeringen på vänster sida.

1. Välj **Lägg till fördefinierad entitet**och välj `geo` sedan i sökfältet för att filtrera de fördefinierade entiteterna.

    > [!div class="mx-imgBorder"]
    > ![Lägg till geografiV2 fördefinierad entitet till app](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Markera kryssrutan och välj **Klar**.
1. I listan **Entiteter** väljer du **geographyV2** för att öppna den nya entiteten.
1. Lägg till `Origin`två `Destination`roller och .

    > [!div class="mx-imgBorder"]
    > ![Lägga till roller i fördefinierad entitet](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

1. Välj **Avsikter** från navigering på vänster sida och välj sedan avsikten **MoveEmployeeToCity.** Lägg märke till att ortnamnen är märkta med den fördefinierade entiteten **geographyV2**.
1. Välj **paletten Entitet**i kontextverktygsfältet .

    > [!div class="mx-imgBorder"]
    > ![Välj entitetspalett i verktygsfältet Innehåll](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Välj den fördefinierade entiteten, **geographyV2**, välj sedan **entitetsinspektören**.
1. Välj en roll, **Mål**i **entitetsinspektören.** Detta ändrar muspekaren. Använd markören för att märka texten i alla yttranden som är målplatsen.

    > [!div class="mx-imgBorder"]
    > ![Välj roll i entitetspalett](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Återgå till **entitetsinspektören**, ändra till rollen till **Origin**. Använd markören för att märka texten i alla yttranden som är ursprungsplatsen.

## <a name="add-example-utterances-to-the-none-intent"></a>Lägga till exempelyttranden i avsikten Ingen

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att avsiktsändringarna kan testas

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att frågor kan köras mot den tränade modellen från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Gå till slutet av webbadressen i adressfältet och ange `Please move Carl Chamerlin from Tampa to Portland`. Den senaste frågesträngparametern är `q`, uttrycksfrågan . **query** Det här uttrycket är inte samma sak som någon av de märkta `MoveEmployee` yttrandena så det är ett bra test och bör returnera avsikten med den extraherade entiteten.

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

    Rätt avsikt förutses och entitetsmatrisen har både ursprungs- och målrollerna i motsvarande **entitetsegenskap.**

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Begrepp för entiteter](luis-concept-entity-types.md)
* [Roller begrepp](luis-concept-roles.md)
* [Lista över fördefinierade entiteter](luis-reference-prebuilt-entities.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)
* [Roller](luis-concept-roles.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapades en ny avsikt, och exempelyttranden lades till för sammanhangsmässigt inlärda data för ursprungs- och målplatser. När appen har tränats och publicerats kan ett klientprogram använda den informationen för att skapa en flyttningsbiljett med relevant information.

> [!div class="nextstepaction"]
> [Lär dig hur du lägger till en sammansatt entitet](luis-tutorial-composite-entity.md)
