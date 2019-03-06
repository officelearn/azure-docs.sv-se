---
title: Fördefinierade avsikter och entiteter
titleSuffix: Azure Cognitive Services
description: I den här självstudien får du lägga till fördefinierade avsikter och entiteter i för att snabbt få avsiktsförutsägelse och dataextrahering. Du behöver inte märka upp några yttranden med fördefinierade entiteter. Entiteten identifieras automatiskt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: cf16f27a8d39871491b7cf46a509b9714a669667
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873822"
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

    [![Skärmbild av dialogrutan för fördefinierade avsikter med Utilities (Verktyg) i sökrutan](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. Välj följande avsikter och välj **Done** (Klar): 

    * Utilities.Cancel (Verktyg.Avbryt)
    * Utilities.Confirm (Verktyg.Bekräfta)
    * Utilities.Help (Verktyg.Hjälp)
    * Utilities.StartOver (Verktyg.Börja_om)
    * Utilities.Stop (Verktyg.Stoppa)

    Dessa avsikter är till hjälp när du ska avgöra var i en konversation användaren är och vad de ber dig att göra. 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Lägg till fördefinierade entiteter för att få hjälp med vanlig extrahering av datatyp

LUIS har flera fördefinierade entiteter för extrahering av data. 

1. Välj **Entities** (Entiteter) på den vänstra navigeringsmenyn.

1. Välj knappen för att **lägga till en fördefinierad entitet**.

1. Välj följande entiteter från listan över fördefinierade entiteter och välj sedan **Klart**:

    * **[PersonName](luis-reference-prebuilt-person.md)** 
    * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

    ![Skärmbild på dialogrutan för fördefinierade entiteter med nummer markerat](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

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
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
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

