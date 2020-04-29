---
title: 'Självstudie: kontextuella data med roller – LUIS'
description: Hitta relaterade data baserat på kontext. Till exempel är ett ursprung och målplatser för en fysisk flytt från en byggnad och ett kontor till en annan byggnad och ett annat kontor relaterade.
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475819"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Självstudie: extrahera sammanhangsbaserade relaterade data från en uttryck

I den här självstudien hittar du relaterade datadelar baserat på kontext. Till exempel ursprung och mål platser för överföring från en stad till en annan. Båda datadelarna kan krävas, och de är relaterade till varandra.

En roll kan användas med en fördefinierad eller anpassad entitetstyp och används i båda exemplen yttranden och Patterns.

**I den här guiden får du lära dig att:**

> [!div class="checklist"]
> * Skapa ny app
> * Lägga till avsikt
> * Hämta information om ursprung och mål med hjälp av roller
> * Träna
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

1. Logga in på [Luis **Preview** Portal](https://preview.luis.ai).

1. Välj **+ ny app för konversation**, ange namnet `HumanResources` och behåll standard kulturen, **engelska**. Lämna beskrivningen och förutsägelse resursen tom. Välj **Klar**.

## <a name="create-an-intent-to-move-employees-between-cities"></a>Skapa en avsikt att flytta medarbetare mellan städer

Avsikten är att klassificera användar yttranden baserat på användarens avsikt, som bestäms från den naturliga språk texten.

För att klassificera en uttryck behöver avsikten exempel på användar-yttranden som ska klassificeras med det här syftet.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Välj **+ skapa**.

1. Ange `MoveEmployeeToCity` i popup-dialogrutan och välj sedan **Done** (Klar).

    > [!div class="mx-imgBorder"]
    > ![Skärmbild på dialogrutan Create new intent (Skapa ny avsikt)](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Lägg till flera exempel yttranden i det här syftet som du förväntar dig att en användare ska ställa.

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
    > ![Skärmbild på LUIS-appen med nya talindata i avsikten MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Lägg till fördefinierad entitet geographyV2

Den fördefinierade entiteten **geographyV2**, extraherar plats information, inklusive Orts namn. Eftersom yttranden har två Orts namn, relaterade till varandra i sammanhanget, använder du roller för att extrahera kontexten.

1. Välj **entiteter** från den vänstra navigeringen.

1. Välj **+ Lägg till fördefinierad entitet**och `geo` ange sedan i Sök fältet för att filtrera de förinställda entiteterna.

    > [!div class="mx-imgBorder"]
    > ![Lägg till geographyV2-fördefinierad entitet i appen](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Markera kryss rutan och välj **färdig**.

## <a name="add-roles-to-prebuilt-entity"></a>Lägg till roller i fördefinierad entitet

1. I listan **entiteter** väljer du **geographyV2** för att öppna den nya entiteten.
1. Om du vill lägga till en **+** roll väljer du och lägger till följande `Origin`två roller `Destination`:, och.

    > [!div class="mx-imgBorder"]
    > ![Lägg till roller i fördefinierad entitet](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>Etikett för entitets-roller i exempel yttranden

1. Välj **avsikter** från navigeringen till vänster och välj sedan **MoveEmployeeToCity** avsikt. Lägg märke till att Orts namnen är märkta med den fördefinierade entiteten **geographyV2**.
1. I verktygsfältet kontext väljer du **paletten entitet** med _Penn ikonen_.

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

Välj **träna**för att träna appen. Träningen tillämpar ändringarna, till exempel nya entiteter och den märkta yttranden, i den aktiva modellen.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicera appen för att få åtkomst till den från HTTP-slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. Gå till slutet av webb adressen i adress fältet och Ersätt _YOUR_QUERY_HERE_ med `Please move Carl Chamerlin from Tampa to Portland`.

Den här uttryck är inte samma som någon av de märkta yttranden, så det är ett lyckat test och ska returnera `MoveEmployee` avsikten med den extraherade enheten.

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

    The correct intent is predicted and the entities array has both the origin and destination roles in the corresponding **entities** property.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Entiteter-koncept](luis-concept-entity-types.md)
* [Roll koncept](luis-concept-roles.md)
* [Lista med färdiga entiteter](luis-reference-prebuilt-entities.md)
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)
* [Roller](luis-concept-roles.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapades en ny avsikt, och exempelyttranden lades till för sammanhangsmässigt inlärda data för ursprungs- och målplatser. När appen har tränats och publicerats kan ett klientprogram använda den informationen för att skapa en flyttningsbiljett med relevant information.

> [!div class="nextstepaction"]
> [Lär dig hur du lägger till en sammansatt entitet](luis-tutorial-composite-entity.md)
