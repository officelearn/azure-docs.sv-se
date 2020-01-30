---
title: 'Självstudie: granska slut punkt yttranden-LUIS'
titleSuffix: Azure Cognitive Services
description: I den här självstudien får du förbättra app-förutsägelserna genom att verifiera eller korrigera yttranden som mottagits via LUIS HTTP-slutpunkten som LUIS är osäker på. I vissa yttranden kan avsikten behöva verifieras och i vissa kan du behöva verifiera entiteter.
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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843981"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Självstudie: åtgärda osäker förutsägelse genom att granska slut punkts yttranden
I den här självstudien får du förbättra appens förutsägelser genom att verifiera eller korrigera yttranden, som tas emot via HTTPS-slutpunkten LUIS, som LUIS är osäker på. Du bör granska slut punkt yttranden som en vanlig del av ditt schemalagda LUIS-underhåll.

Den här gransknings processen gör det möjligt för LUIS att lära sig din app-domän. LUIS väljer den yttranden som visas i gransknings listan. Följande gäller för listan:

* Den är specifik för appen.
* Den är avsedd att förbättra noggrannheten hos appens förutsägelse.
* Den bör granskas regelbundet.

Genom att granska slutpunktsyttranden verifierar eller korrigerar du det yttrandets förutsagda avsikt.

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera exempelappen
> * Granska slutpunktsyttranden
> * Träna och publicera appen
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importera exempelappen

Använd följande steg för att importera en app.

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true).

1. På [Luis-portalen för förhands granskning](https://preview.luis.ai)importerar du. JSON-filen till en ny app.

1. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `review`.

    > [!TIP]
    > Att klona till en ny version är en bra metod innan du ändrar appen. När du är klar med en version exporterar du versionen (som en. JSON-eller. lu-fil) och kontrollerar filen i käll kontroll systemet.


1. Välj **träna**för att träna appen.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicera appen för att få åtkomst till den från HTTP-slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Lägg till yttranden i slut punkten

I den här appen har du avsikter och entiteter, men du har inte någon slut punkts användning. Den här slut punkts användningen krävs för att förbättra appen med slut punkts uttryck granskning.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Använd slutpunkten för att lägga till följande talindata.

    |Slut punkt uttryck|Anpassad avsikt|
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

Granska slut punkts yttranden för korrekt justerat syfte. Även om det finns en enda pool av yttranden för att granska alla versioner, lägger processen för att justera avsikten till exempel uttryck endast till den aktuella _aktiva modellen_ .

1. I avsnittet **build** i portalen väljer du **Granska slut punkt yttranden** i det vänstra navigerings fältet. Listan filtreras efter avsikten **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av knappen granska slut punkt yttranden i det vänstra navigerings fältet](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Den här uttryck, `I'm looking for a job with Natural Language Processing`, har inte rätt avsikt.

1.  Om du vill justera den här uttryck väljer du rätt **justerat avsikt** för `GetJobInformation`på raden uttryck. Lägg till den ändrade uttryck i appen genom att markera bock markeringen.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av knappen granska slut punkt yttranden i det vänstra navigerings fältet](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Granska återstående yttranden i det här syftet och korrigera den justerade avsikten vid behov. Använd den inledande uttryck-tabellen i den här självstudien för att visa den justerade avsikten.

    Yttranden-listan för **gransknings slut punkt** bör inte längre ha den korrigerade yttranden. Om fler yttranden visas fortsätter du att arbeta genom listan och korrigerar Justerings avsikt tills listan är tom.

    All korrigering av enhets etiketter görs när avsikten är justerad från sidan information om avsikt.

1. Träna och publicera appen igen.

## <a name="get-intent-prediction-from-endpoint"></a>Hämta förutsägelse för avsikt från slut punkt

Om du vill kontrol lera det korrekt justerade exemplet yttranden förbättrad appens förutsägelse kan du försöka med en uttryck nära den korrigerade uttryck.

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Gå till slutet av URL:en i adressen och ange `Are there any natural language processing jobs in my department right now?`. Den sista frågesträngsparametern är `q`, yttrande**frågan**.

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

   Nu när yttranden är korrekt justerade är rätt avsikt att förutsägas med en **hög poäng**.

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
