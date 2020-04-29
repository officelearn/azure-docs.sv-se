---
title: 'Snabb start: skapa app-LUIS'
description: Den här snabb starten visar hur du skapar en LUIS-app som använder den `HomeAutomation` färdiga domänen för att aktivera och inaktivera lampor och apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.
ms.topic: quickstart
ms.date: 03/24/2020
ms.openlocfilehash: de6cf5e95ee63fc9500cf1b5edab78597bdb18af
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80287806"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snabbstart: Använda en färdig hemautomatiseringsapp

I den här snabbstarten skapar du en LUIS-app som använder den fördefinierade domänen `HomeAutomation` för att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Skapa en ny app
Du kan skapa och hantera dina appar på **Mina appar**.

1. I listan Mina appar väljer du **+ ny app för konversation**.

1. I dialog rutan namnger du ditt program `Home Automation`. Välj **engelska** som kultur. Beskrivningen är valfri och används inte för redigering eller förutsägelse. Förutsägelse resursen är också valfri när du skapar en LUIS-app. Välj **Klar**.

    LUIS skapar appen. När du publicerar din app till produktion bör du tilldela en förutsägelse resurs så att din app kan hantera många begär Anden.

    ![I dialog rutan namnger du appens hem automatisering](./media/create-new-app-details.png)

    >[!NOTE]
    >Kulturen kan inte ändras när appen har skapats.

## <a name="add-prebuilt-domain"></a>Lägga till fördefinierad domän

Välj **förinställda domäner** och Sök sedan efter **HomeAutomation**. Välj **Lägg till domän** på HomeAutomation-kortet.

![Välj "förinställda domäner" och Sök efter "HomeAutomation". Välj Lägg till domän på HomeAutomation-kortet.](media/luis-quickstart-new-app/home-automation.png)

När domänen har lagts till visar rutan för den fördefinierade domänen knappen **Remove domain** (Ta bort domän).

## <a name="intents-and-entities"></a>Avsikter och entiteter

Välj **avsikter** för att granska HomeAutomation domän avsikter. De fördefinierade domän exemplen har ett exempel på yttranden.

![Skärm bild av listan med HomeAutomation-intentor](media/luis-quickstart-new-app/home-automation-intents.png "Skärm bild av listan med HomeAutomation-intentor")

> [!NOTE]
> **None** (Ingen) är en avsikt som tillhandahålls av alla LUIS-appar. Du kan använda den till att hantera yttranden som inte motsvarar funktioner som finns i din app.

Välj avsikten **HomeAutomation.TurnOff** (stänga av). Du kan se att avsikten innehåller en lista med yttranden som är märkta med entiteter.

[![Skärmbild av avsikten HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Skärmbild av avsikten HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testa din app
När du har tränat appen kan du testa den.

1. Välj **test** i det övre högra navigerings fönstret. 1. Skriv ett test-uttryck `Turn off the lights` som i det interaktiva test fönstret och tryck på RETUR.

    ```
    Turn off the lights
    ```

    Kontrollera att avsikten med högst poäng motsvarar den avsikt du förväntade dig för varje testyttrande.

    I det här exemplet `Turn off the lights` identifieras korrekt som det främsta bedömnings syftet för **HomeAutomation. TurnOff**.

    ![Skärmbild av testpanel med yttranden markerade](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Välj **Granska** för att granska mer information om förutsägelsen.

    ![Skärm bild av test panelen med gransknings information](media/luis-quickstart-new-app/test.png)

1. Stäng test fönstret.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Fråga v3 API förutsägelse-slutpunkten

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. I adress fältet i webbläsaren kontrollerar du att följande namn och värde fält finns i URL: en för frågesträngen. Om de inte finns i frågesträngen lägger du till dem:

    |Namn/värde-par|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. I webbläsarens Adress fält går du till slutet av URL: en och anger `turn off the living room light` värdet för _fråga_ och trycker på RETUR.

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

    Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du kan anropa slutpunkten via kod:

> [!div class="nextstepaction"]
> [Anropa en LUIS-slutpunkt med kod](luis-get-started-cs-get-intent.md)
