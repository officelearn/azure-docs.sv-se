---
title: 'Självstudiekurs: Granska slutpunktsyttranden - LUIS'
titleSuffix: Azure Cognitive Services
description: I den här självstudien kan du förbättra appförutsägelser genom att verifiera eller korrigera yttranden som tas emot via LUIS HTTP-slutpunkten som LUIS är osäker på. I vissa yttranden kan avsikten behöva verifieras och i vissa kan du behöva verifiera entiteter.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 06f51ca83449b39861e7565cc9accc29efbece3f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843981"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Självstudiekurs: Åtgärda osäkra förutsägelser genom att granska slutpunktsyttranden
I den här självstudien kan du förbättra appförutsägelser genom att verifiera eller korrigera yttranden, som tas emot via LUIS HTTPS-slutpunkten, som LUIS är osäker på. Du bör granska slutpunktsyttranden som en vanlig del av ditt schemalagda LUIS-underhåll.

Med den här granskningsprocessen kan LUIS lära sig din appdomän. LUIS väljer de yttranden som visas i granskningslistan. Följande gäller för listan:

* Den är specifik för appen.
* Den är avsedd att förbättra noggrannheten hos appens förutsägelse.
* Den bör granskas regelbundet.

Genom att granska slutpunktsyttranden verifierar eller korrigerar du det yttrandets förutsagda avsikt.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera exempelappen
> * Granska slutpunktsyttranden
> * Appen Träna och publicera
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importera exempelappen

Använd följande steg för att importera en app.

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true).

1. Importera FILEN .json till en ny app i [luis-portalen.](https://preview.luis.ai)

1. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `review`.

    > [!TIP]
    > Kloning till en ny version är en bra metod innan du ändrar appen. När du är klar med en version exporterar du versionen (som en .json- eller .lu-fil) och kontrollerar filen i källkontrollsystemet.


1. Om du vill träna appen väljer du **Träna**.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicera appen för att komma åt den från HTTP-slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Lägga till yttranden vid slutpunkten

I den här appen har du avsikter och entiteter men du har ingen slutpunktsanvändning. Den här slutpunktsanvändningen krävs för att förbättra appen med granskningen av slutpunktsutsikten.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Använd slutpunkten för att lägga till följande talindata.

    |Slutpunktsutseende|Justerad avsikt|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    Det finns en enda pool med yttranden att granska, oavsett vilken version du aktivt redigerar eller vilken version av appen som publicerades vid slutpunkten.

## <a name="review-endpoint-utterances"></a>Granska slutpunktsyttranden

Granska slutpunktsyttrandena för korrekt justerad avsikt. Det finns en enda grupp yttranden som ska granskas i alla versioner, men processen för att justera avsikten på rätt sätt lägger till exempelutsikteringen till den aktuella _aktiva modellen._

1. I avsnittet **Skapa** i portalen väljer du **Granska slutpunktsyttranden** från den vänstra navigeringen. Listan filtreras efter avsikten **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av knappen Granska slutpunktstalindata i det vänstra navigeringsfönstret](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Detta uttryck, `I'm looking for a job with Natural Language Processing`, är inte i rätt avsikt.

1.  Om du vill justera det här uttrycket markerar du `GetJobInformation`rätt justerad avsikt för på **uttrycksraden** . Lägg till det ändrade uttrycket i appen genom att markera bocken.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av knappen Granska slutpunktstalindata i det vänstra navigeringsfönstret](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Granska de återstående yttrandena i den här avsikten och korrigera den justerade avsikten efter behov. Använd den första uttryckstabellen i den här självstudien för att visa den justerade avsikten.

    Listan **Granska slutpunktsyttranden** bör inte längre ha de korrigerade yttrandena. Om fler yttranden visas fortsätter du att arbeta igenom listan och korrigerar justerade avsikter tills listan är tom.

    All korrigering av entitetsmärkning görs när avsikten har justerats från sidan Avsiktsinformation.

1. Träna och publicera appen igen.

## <a name="get-intent-prediction-from-endpoint"></a>Hämta avsiktsprediktion från slutpunkten

Om du vill kontrollera att de korrekt justerade exempelyttrandena har förbättrat appens förutsägelse provar du ett uttryck nära det korrigerade uttrycket.

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Gå till slutet av URL:en i adressen och ange `Are there any natural language processing jobs in my department right now?`. Den senaste frågesträngparametern är `q`, uttrycksfrågan . **query**

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
   ```

   Nu när de osäkra yttrandena är korrekt justerade, förutsades rätt avsikt med en **hög poäng**.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Går det att ersätta granskning genom att lägga till fler yttranden?
Du undrar kanske varför man inte bara kan lägga till fler exempelyttranden. Vad är syftet med att granska slutpunktsyttranden? I en riktig LUIS-app kommer slutpunktsyttranden från användare med ordval och ordningsföljd som du inte har använt än. Om du hade använt samma ordval och ordningsföljd skulle den ursprungliga förutsägelsen ha haft ett högre procenttal.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Varför är den högsta avsikten på yttrandelistan?
Vissa av slutpunktsyttrandena har en hög förutsägelsepoäng i granskningslistan. Du behöver ändå granska och kontrollera de yttrandena. De finns med i listan eftersom nästföljande högsta avsikt hade en poäng som var för nära avsikten med högst poäng. Du vill ha en skillnad på ungefär 15 % mellan de två översta avsikterna.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du granskat yttranden som skickats vid slutpunkten och som LUIS inte kunnat tolka säkert. När de här yttrandena har verifierats och flyttas till rätt avsikter som exempelyttranden blir förutsägelserna i LUIS bättre.

> [!div class="nextstepaction"]
> [Lär dig hur du använder mönster](luis-tutorial-pattern.md)
