---
title: 'Snabb start: skapa app-LUIS'
titleSuffix: Azure Cognitive Services
description: Skapa en LUIS-app där den fördefinierade domänen `HomeAutomation` används till att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 748c51e74db20ac101dc2dff0d924567acded114
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703243"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snabbstart: Använda fördefinierad hemautomatisering

I den här snabbstarten skapar du en LUIS-app som använder den fördefinierade domänen `HomeAutomation` för att släcka lampor och stänga av apparater. Den fördefinierade domänen ger dig avsikter, entiteter och exempelyttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet.

## <a name="prerequisites"></a>Förutsättningar

För den här artikeln behöver du ett kostnadsfritt LUIS-konto som skapas på LUIS-portalen på [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Skapa en ny app
Du kan skapa och hantera dina appar på **Mina appar**. 

1. Välj **Create new app** (Skapa ny app).

    [![Skärmbild av applista](media/luis-quickstart-new-app/app-list.png "Skärmbild av applista")](media/luis-quickstart-new-app/app-list.png)

1. Namnge appen ”Home Automation” (Hemautomatisering) i dialogrutan.

    [![Skärmbild av popup-fönstret Skapa ny app](media/luis-quickstart-new-app/create-new-app-dialog.png "Skärmbild av popup-fönstret Skapa ny app")](media/luis-quickstart-new-app/create-new-app-dialog.png)

1. Välj programkultur. Välj engelska för appen Home Automation. Välj sedan **Done** (Klar). LUIS skapar appen Home Automation. 

    >[!NOTE]
    >Kulturen kan inte ändras när appen har skapats. 

## <a name="add-prebuilt-domain"></a>Lägga till fördefinierad domän

Välj **Prebuilt domains** (Fönsterruta domäner) i navigeringsfönstret till vänster. Sök sedan efter ”Home”. Välj **Add domain** (Lägg till domän).

[![Skärmbild av domänen för hemautomatisering som anges i en fördefinierad domänmeny](media/luis-quickstart-new-app/home-automation.png "Skärmbild av domänen för hemautomatisering som anges i en fördefinierad domänmeny")](media/luis-quickstart-new-app/home-automation.png)

När domänen har lagts till visar rutan för den fördefinierade domänen knappen **Remove domain** (Ta bort domän).

[![Skärmbild av domänen för hemautomatisering med knappen Ta bort](media/luis-quickstart-new-app/remove-domain.png "Skärmbild av domänen för hemautomatisering med knappen Ta bort")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Avsikter och entiteter

Välj **Intents** i navigeringsfönstret till vänster för att granska avsikter för domänen HomeAutomation. Varje avsikt har exempelyttranden.

![Skärm bild av listan med HomeAutomation-intentor](media/luis-quickstart-new-app/home-automation-intents.png "skärm bild av listan över HomeAutomation")

> [!NOTE]
> **None** (Ingen) är en avsikt som tillhandahålls av alla LUIS-appar. Du kan använda den till att hantera yttranden som inte motsvarar funktioner som finns i din app. 

Välj avsikten **HomeAutomation.TurnOff** (stänga av). Du kan se att avsikten innehåller en lista med yttranden som är märkta med entiteter.

[![Skärmbild av avsikten HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Skärmbild av avsikten HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testa din app
När du har tränat appen kan du testa den. Välj **Test** i det övre navigeringsfältet. Skriv ett testyttrande som ”Turn off the lights” (Släck lamporna) i fönstret Interactive Testing (Interaktiv testning) och tryck på RETUR. 

```
Turn off the lights
```

Kontrollera att avsikten med högst poäng motsvarar den avsikt du förväntade dig för varje testyttrande.

I det här exemplet identifieras `Turn off the lights` korrekt som det främsta bedömnings syftet för **HomeAutomation. TurnOff**.

[![Skärmbild av testpanelen med ett yttrande markerat](media/luis-quickstart-new-app/test.png "Skärmbild av testpanelen med ett yttrande markerat")](media/luis-quickstart-new-app/test.png)


Välj **Granska** för att granska mer information om förutsägelsen.

![Skärm bild av test panelen med uttryck markerat](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Välj **Test** igen för att dölja testfönstret. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>Fråga v2-API förutsägelse slut punkt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Gå till slutet av webbadressen, skriv `turn off the living room light` och tryck på Retur. 

    #### <a name="v2-prediction-endpointtabv2"></a>[V2-förutsägelse slut punkt](#tab/V2)

    `https://<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/<appID>?subscription-key=<YOUR_KEY>&**q=<user-utterance-text>**`

    Webbläsaren visar API-versionen **v2** för JSON-svaret på http-slutpunkten.

    ```json
    {
      "query": "turn off the lights",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.995867
      },
      "entities": [
        {
          "entity": "lights",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 13,
          "endIndex": 18,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
    #### <a name="v3-prediction-endpointtabv3"></a>[V3 förutsägelse slut punkt](#tab/V3)

    För en [v3 API-fråga](luis-migration-api-v3.md)i webbläsaren ändrar du Get-metoden https-begäran och ändrar värdena inom vinkelparenteser för dina egna värden.     

    `https://<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/<appID>/**slots**/**production**/**predict**?subscription-key=<YOUR_KEY>&**query=<user-utterance-text>**`

    ```json
    {
        "query": "turn off the lights",
        "prediction": {
            "normalizedQuery": "turn off the lights",
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.99649024
                }
            },
            "entities": {
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ]
            }
        }
    }
    ```


    Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).
    
    * * * 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du kan anropa slutpunkten via kod:

> [!div class="nextstepaction"]
> [Anropa en LUIS-slutpunkt med kod](luis-get-started-cs-get-intent.md)
