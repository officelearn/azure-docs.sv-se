---
title: 'Snabbstart: Få uppsåt med webbläsaren - LUIS'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten använder du en tillgänglig offentlig LUIS-app för att avgöra en användares avsikt från konversationstext i en webbläsare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: e06bb4c09b3ebab25c0c0ef8ac5c51f6842f34cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987962"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Snabbstart: Få uppsåt med en webbläsare

Om du vill förstå vad som returneras från en LUIS-slutpunkt för förutsägelser kan du granska resultatet i en webbläsare.

## <a name="prerequisites"></a>Krav

För att kunna fråga en offentlig app behöver du:

* Din egen språkförståelse (LUIS) Författande eller förutsägelsenyckel som kan erhållas från [LUIS Portal (Preview)](https://preview.luis.ai/). Om du inte redan har en prenumeration för att skapa en nyckel kan du registrera dig för ett [gratis konto](https://azure.microsoft.com/free/).
* Den offentliga appens ID: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.

## <a name="use-the-browser-to-see-predictions"></a>Använd webbläsaren för att se förutsägelser

1. Öppna en webbläsare.
1. Använd de fullständiga webbadresserna `YOUR-KEY` nedan och ersätt med din egen LUIS-redigerings- eller förutsägelsenyckel. Begäranden är GET-begäranden och inkluderar auktoriseringen, med nyckeln LUIS-redigering eller förutsägelse, som en frågesträngparameter.

    #### <a name="v3-prediction-request"></a>[Begäran om V3-förutsägelse](#tab/V3-1-1)


    Formatet på V3-URL:en för en **GET-slutpunktsbegäran** (efter fack) är:

    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-request"></a>[Begäran om V2-förutsägelse](#tab/V2-1-2)

    Formatet på V2-URL:en för en **GET-slutpunktsbegäran** är:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Klistra in URL:en i ett webbläsarfönster och tryck på Retur. Du ser ett JSON-resultat i webbläsaren som indikerar att LUIS har identifierat avsikten `HomeAutomation.TurnOn` som troligaste avsikt och entiteten `HomeAutomation.Operation` med värdet `on`.

    #### <a name="v3-prediction-response"></a>[V3 förutsägelse svar](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[V2 förutsägelse svar](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Om du vill visa alla avsikter lägger du till lämplig frågesträngparameter.

    #### <a name="v3-prediction-endpoint"></a>[Slutpunkt för V3-förutsägelse](#tab/V3-3-1)

    Lägg `show-all-intents=true` till i slutet av frågesträngen för att **visa alla avsikter:**

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[Slutpunkt för V2-förutsägelse](#tab/V2)

    Lägg `verbose=true` till i slutet av frågesträngen för att **visa alla avsikter:**

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Skapa en app i LUIS-portalen](get-started-portal-build-app.md)
