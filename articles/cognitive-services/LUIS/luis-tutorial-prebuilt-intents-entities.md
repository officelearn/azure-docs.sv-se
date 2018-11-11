---
title: 'Självstudie 2: Fördefinierade avsikter och entiteter – använd fördefinierade vanliga yttranden – extrahera vanliga data i LUIS'
titleSuffix: Azure Cognitive Services
description: Lägg till fördefinierade avsikter och entiteter i Human Resources-självstudieappen för att snabbt få avsiktsförutsägelse och dataextrahering. Du behöver inte märka upp några yttranden med fördefinierade entiteter. Entiteten identifieras automatiskt.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d73284ecf40b2dfcd4954c1ddee82bf92a8931b9
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281803"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>Självstudie 2: Identifiera vanliga avsikter och entiteter
I den här självstudien ändrar du Human Resources-appen. Lägg till fördefinierade avsikter och entiteter i Human Resources-självstudieappen för att snabbt få avsiktsförutsägelse och dataextrahering. Du behöver inte märka några yttranden med fördefinierade entiteter eftersom entiteten identifieras automatiskt.

Fördefinierade modeller av vanliga ämnesdomäner och datatyper hjälper dig att snabbt bygga din modell och ger ett exempel på hur en modell ser ut. 

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Använda en befintlig självstudieapp
> * Lägga till fördefinierade avsikter 
> * Lägg till fördefinierade entiteter 
> * Träna 
> * Publicera 
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använda en befintlig app
Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Om du inte har appen HumanResources från föregående självstudie gör du så här:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `prebuilts`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser. 

## <a name="add-prebuilt-intents"></a>Lägga till fördefinierade avsikter
LUIS har flera fördefinierade avsikter som hjälper dig med vanliga användaravsikter.  

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **Lägg till fördefinierad avsikt**. 

3. Sök efter `Utilities`. 

    [ ![Skärmbild av dialogrutan för fördefinierade avsikter med Utilities (Verktyg) i sökrutan](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Välj följande avsikter och välj **Done** (Klar): 

    * Utilities.Cancel (Verktyg.Avbryt)
    * Utilities.Confirm (Verktyg.Bekräfta)
    * Utilities.Help (Verktyg.Hjälp)
    * Utilities.StartOver (Verktyg.Börja_om)
    * Utilities.Stop (Verktyg.Stoppa)


## <a name="add-prebuilt-entities"></a>Lägg till fördefinierade entiteter
LUIS har flera fördefinierade entiteter för extrahering av data. 

1. Välj **Entities** (Entiteter) på den vänstra navigeringsmenyn.

2. Välj knappen **Manage prebuilt entity** (Hantera fördefinierad entitet).

3. Välj **number** (nummer) och **datetimeV2** i listan över fördefinierade entiteter och sedan **Done** (Klar).

    ![Skärmbild på dialogrutan för fördefinierade entiteter med nummer markerat](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>Träna

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicera

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Gå till slutet av webbadressen i webbläsarens adressfält och ange `I want to cancel on March 3`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. 

    ```JSON
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    Resultatet förutsåg avsikten Utilities.Cancel (Verktyg.Avbryt) och extraherade datumet March 3 (3 mars) och numret 3. 

    Det finns två värden för March 3 (3 mars) eftersom yttrande inte anger om March 3 (3 mars) är i dåtid eller framtid. Det är upp till klientprogrammet att göra ett antagande eller be om förtydligande, om det behövs. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Genom att lägga till fördefinierade avsikter och entiteter kan klientprogrammet bestämma vanliga användaravsikter och extrahera vanliga datatyper. 

> [!div class="nextstepaction"]
> [Lägga till en entitet för reguljärt uttryck i appen](luis-quickstart-intents-regex-entity.md)

