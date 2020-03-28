---
title: 'Snabbstart: skapa app - LUIS'
description: Den här snabbstarten visar hur du skapar en `HomeAutomation` LUIS-app som använder den fördefinierade domänen för att tända och stänga av lampor och apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.
ms.topic: quickstart
ms.date: 03/24/2020
ms.openlocfilehash: de6cf5e95ee63fc9500cf1b5edab78597bdb18af
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287806"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snabbstart: Använda en färdig hemautomatiseringsapp

I den här snabbstarten skapar du en LUIS-app som använder den fördefinierade domänen `HomeAutomation` för att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Skapa en ny app
Du kan skapa och hantera dina appar på **Mina appar**.

1. I listan Mina appar väljer du **+ Ny app för konversation**.

1. Namnge programmet `Home Automation`i dialogrutan . Välj **engelska** som kultur. Beskrivningen är valfri och används inte för redigering eller förutsägelse. Förutsägelseresursen är också valfri när du skapar en LUIS-app. Välj **Done** (Klar).

    LUIS skapar appen. När du publicerar appen i produktion bör du tilldela en förutsägelseresurs så att appen kan hantera många begäranden.

    ![I dialogrutan namnger du programmet "Home Automation"](./media/create-new-app-details.png)

    >[!NOTE]
    >Kulturen kan inte ändras när appen har skapats.

## <a name="add-prebuilt-domain"></a>Lägga till fördefinierad domän

Välj **Fördefinierade domäner** och sök sedan efter **HomeAutomation**. Välj **Lägg till domän** på HomeAutomation-kortet.

![Välj "Fördefinierade domäner" och sök sedan efter "HomeAutomation". Välj Lägg till domän på HomeAutomation-kortet.](media/luis-quickstart-new-app/home-automation.png)

När domänen har lagts till visar rutan för den fördefinierade domänen knappen **Remove domain** (Ta bort domän).

## <a name="intents-and-entities"></a>Avsikter och entiteter

Välj **Avsikter om** du vill granska avsikter med HomeAutomation-domänen. De fördefinierade domänavsikterna har exempelyttranden.

![Skärmbild av listan över avsikter i HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Skärmbild av listan över avsikter i HomeAutomation")

> [!NOTE]
> **None** (Ingen) är en avsikt som tillhandahålls av alla LUIS-appar. Du kan använda den till att hantera yttranden som inte motsvarar funktioner som finns i din app.

Välj avsikten **HomeAutomation.TurnOff** (stänga av). Du kan se att avsikten innehåller en lista med yttranden som är märkta med entiteter.

[![Skärmbild av avsikten HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Skärmbild av avsikten HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testa din app
När du har tränat appen kan du testa den.

1. Välj **Testa** i navigeringen längst upp till höger. 1. Skriv ett testuttryck som `Turn off the lights` i den interaktiva testfönstret och tryck på Retur.

    ```
    Turn off the lights
    ```

    Kontrollera att avsikten med högst poäng motsvarar den avsikt du förväntade dig för varje testyttrande.

    I det `Turn off the lights` här exemplet identifieras den högsta poängmetoden för **HomeAutomation.TurnOff**.

    ![Skärmbild av testpanel med yttranden markerade](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Välj **Granska** om du vill granska mer information om förutsägelsen.

    ![Skärmbild av testpanelen med inspektionsinformation](media/luis-quickstart-new-app/test.png)

1. Stäng testfönstret.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Fråga slutpunkten för V3 API-förutsägelse

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Kontrollera att följande namn och värdestaplar finns i URL:en i url:en i webbläsarens adressfält. Om de inte finns i frågesträngen lägger du till dem:

    |Namn/värdepar|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Gå till slutet av URL:en i webbläsarens adressfält och ange `turn off the living room light` _frågevärdet_ och tryck sedan på Retur.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.907323956,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du kan anropa slutpunkten via kod:

> [!div class="nextstepaction"]
> [Anropa en LUIS-slutpunkt med kod](luis-get-started-cs-get-intent.md)
