---
title: 'Självstudie: granska slut punkt yttranden-LUIS'
description: I den här självstudien får du förbättra app-förutsägelserna genom att verifiera eller korrigera yttranden som mottagits via LUIS HTTP-slutpunkten som LUIS är osäker på. I vissa yttranden kan avsikten behöva verifieras och i vissa kan du behöva verifiera entiteter.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/02/2020
ms.openlocfilehash: b8f8fa2cd3c9c22187bb95c55d9de2abb2e8caec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324645"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Självstudie: åtgärda osäker förutsägelse genom att granska slut punkts yttranden
I den här självstudien får du förbättra appens förutsägelser genom att verifiera eller korrigera yttranden, som tas emot via HTTPS-slutpunkten LUIS, som LUIS är osäker på. Du bör granska slut punkt yttranden som en vanlig del av ditt schemalagda LUIS-underhåll.

Den här gransknings processen gör det möjligt för LUIS att lära sig din app-domän. LUIS väljer den yttranden som visas i gransknings listan. Följande gäller för listan:

* Den är specifik för appen.
* Den är avsedd att förbättra noggrannheten hos appens förutsägelse.
* Den bör granskas regelbundet.

Genom att granska slutpunktsyttranden verifierar eller korrigerar du det yttrandets förutsagda avsikt.

**I den här guiden får du lära dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera exempelappen
> * Granska slutpunktsyttranden
> * Träna och publicera appen
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>Ladda ned JSON-fil för appen

Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

## <a name="import-json-file-for-app"></a>Importera JSON-fil för appen


1. I [Luis-portalen](https://www.luis.ai)väljer du **+ ny app för konversation**på sidan **Mina appar** och **importerar sedan som JSON**. Hitta den sparade JSON-filen från föregående steg. Du behöver inte ändra namnet på appen. Välj **färdig**

1. Välj **build** och **avsikt** för att se avsikterna, de viktigaste Bygg stenarna för en Luis-app.

    :::image type="content" source="media/luis-tutorial-review-endpoint-utterances/initial-intents-in-app.png" alt-text="Ändra från sidan versioner till sidan avsikter.":::

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Träna appen att tillämpa enhets ändringarna på appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicera appen för att få åtkomst till den från HTTP-slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Lägg till yttranden i slut punkten

I den här appen har du avsikter och entiteter, men du har inte någon slut punkts användning. Den här slut punkts användningen krävs för att förbättra appen med slut punkts uttryck granskning.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av webb adressen i adress fältet och Ersätt _YOUR_QUERY_HERE_ med yttranden i följande tabell. Skicka uttryck för varje uttryck och få resultatet. Ersätt sedan uttryck i slutet med nästa uttryck.

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

    :::image type="content" source="./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png" alt-text="Ändra från sidan versioner till sidan avsikter.":::

    Den här uttryck, `I'm looking for a job with Natural Language Processing` , finns inte i rätt avsikt, _GetJobInformation_. Det har blivit oförutsägbart som _ApplyForJob_ på grund av likheten mellan jobb namn och verb i båda syftena.

1.  Om du vill justera den här uttryck väljer du rätt **justerat avsikt** för `GetJobInformation` . Lägg till den ändrade uttryck i appen genom att markera bock markeringen.

    Granska återstående yttranden i det här syftet och korrigera den justerade avsikten vid behov. Använd den inledande uttryck-tabellen i den här självstudien för att visa den justerade avsikten.

    Yttranden-listan för **gransknings slut punkt** bör inte längre ha den korrigerade yttranden. Om fler yttranden visas fortsätter du att arbeta genom listan och korrigerar Justerings avsikt tills listan är tom.

    All korrigering av enhets etiketter görs när avsikten är justerad från sidan information om avsikt.

1. Träna och publicera appen igen.

## <a name="get-intent-prediction-from-endpoint"></a>Hämta förutsägelse för avsikt från slut punkt

Om du vill kontrol lera det korrekt justerade exemplet yttranden förbättrad appens förutsägelse kan du försöka med en uttryck nära den korrigerade uttryck.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av webb adressen i adress fältet och Ersätt _YOUR_QUERY_HERE_ med `Are there any natural language processing jobs in my department right now?` .

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.901367366
                },
                "ApplyForJob": {
                    "score": 0.0307973567
                },
                "EmployeeFeedback": {
                    "score": 0.0296942145
                },
                "MoveEmployee": {
                    "score": 0.00739785144
                },
                "FindForm": {
                    "score": 0.00449316856
                },
                "Utilities.Stop": {
                    "score": 0.00417657848
                },
                "Utilities.StartOver": {
                    "score": 0.00407167152
                },
                "Utilities.Help": {
                    "score": 0.003662492
                },
                "None": {
                    "score": 0.00335733569
                },
                "Utilities.Cancel": {
                    "score": 0.002225436
                },
                "Utilities.Confirm": {
                    "score": 0.00107437756
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
                                        "value": "2020-07-02 21:45:50"
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
