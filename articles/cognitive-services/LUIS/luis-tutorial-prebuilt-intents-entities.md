---
title: Förbyggda avsikter och entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: I den här självstudien får du lägga till fördefinierade avsikter och entiteter i för att snabbt få avsiktsförutsägelse och dataextrahering. Du behöver inte märka upp några yttranden med fördefinierade entiteter. Entiteten identifieras automatiskt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 4697bad15a374bed0de08b7cabc5aceaad7f1259
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876711"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Självstudier: Identifiera vanliga avsikter och entiteter

I den här självstudien lägger du till fördefinierade avsikter och entiteter i Human Resources-självstudieappen för att snabbt få avsiktsförutsägelse och dataextrahering. Du behöver inte markera yttranden med förskapade entiteter eftersom entiteten identifieras automatiskt.

Fördefinierade modeller (domäner, avsikter och entiteter) hjälper dig att snabbt bygga din modell.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa ny app
> * Lägga till fördefinierade avsikter 
> * Lägg till fördefinierade entiteter 
> * Träna 
> * Publicera 
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Skapa en ny app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Lägg till flera fördefinierade avsikter som hjälper dig med vanliga användaravsikter

LUIS har flera fördefinierade avsikter som hjälper dig med vanliga användaravsikter.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Välj **Add prebuilt domain intent** (Lägg till fördefinierad domänavsikt). 

1. Sök efter `Utilities`. 

1. Välj alla avsikter och välj **färdig**. Dessa avsikter är till hjälp när du ska avgöra var i en konversation användaren är och vad de ber dig att göra. 

## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Lägg till fördefinierade entiteter för att få hjälp med vanlig extrahering av datatyp

LUIS har flera fördefinierade entiteter för extrahering av data. 

1. Välj **Entities** (Entiteter) på den vänstra navigeringsmenyn.

1. Välj knappen för att **lägga till en fördefinierad entitet**.

1. Välj följande entiteter från listan över fördefinierade entiteter och välj sedan **Klart**:

   * **[PersonName](luis-reference-prebuilt-person.md)** 
   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     ![Skärm bild av antal valda i fördefinierade entiteter-dialog](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

     Med dessa entiteter kan du lägga till namn och plats till ditt klientprogram.

## <a name="add-example-utterances-to-the-none-intent"></a>Lägg till exempelyttranden till avsikten Ingen 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Träna appen så att avsiktsändringarna kan testas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicera appen så att frågor kan köras mot den tränade modellen från slutpunkten

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Hämta avsikts- och entitetsförutsägelser från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Gå till slutet av webbadressen i webbläsarens adressfält och ange `I want to cancel my trip to Seattle to see Bob Smith`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith.",
      "topScoringIntent": {
        "intent": "Utilities.ReadAloud",
        "score": 0.100361854
      },
      "intents": [
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.100361854
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.08102781
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.0398852825
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0277276486
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.0220712926
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0145813478
        },
        {
          "intent": "None",
          "score": 0.012434179
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.0122632384
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.008534077
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.00547111453
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.00152912608
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0005556819
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000169488427
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000149565312
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000141017343
        },
        {
          "intent": "Utilities.Reject",
          "score": 6.27324E-06
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    Resultatet förutsåg Utilities.Cancel-avsikt med 80 % och extraherade data för orten och personnamnet. 


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Relaterad information

Läs mer om fördefinierade modeller:

* [Fördefinierade domäner](luis-reference-prebuilt-domains.md): dessa är vanliga domäner som minskar övergripande LUIS-appredigering
* Fördefinierade avsikter: det här är enskilda avsikter i de vanliga domänerna. Du kan lägga till avsikter individuellt i stället för att lägga till hela domänen.
* [Fördefinierade entiteter](luis-prebuilt-entities.md): de är vanliga datatyper som är användbara för de flesta LUIS-appar.

Lär dig mer om hur du arbetar med LUIS-appen:

* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)

## <a name="next-steps"></a>Nästa steg

Genom att lägga till fördefinierade avsikter och entiteter kan klientprogrammet bestämma vanliga användaravsikter och extrahera vanliga datatyper.  

> [!div class="nextstepaction"]
> [Lägga till en entitet för reguljärt uttryck i appen](luis-quickstart-intents-regex-entity.md)

