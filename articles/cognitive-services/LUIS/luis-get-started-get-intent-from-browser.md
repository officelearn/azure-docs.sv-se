---
title: 'Snabbstart: Fråga efter förutsägelse med webbläsare - LUIS'
description: I den här snabbstarten använder du en tillgänglig offentlig LUIS-app för att avgöra en användares avsikt från konversationstext i en webbläsare.
ms.topic: quickstart
ms.date: 04/21/2020
ms.openlocfilehash: 5ba86882ebf3cb538ad6b865382342fcbd43d27c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769969"
---
# <a name="quickstart-query-prediction-runtime-with-user-text"></a>Snabbstart: Körning för frågeförutsägelse med användartext

Om du vill förstå vad som returneras från en LUIS-slutpunkt för förutsägelser kan du granska resultatet i en webbläsare.

## <a name="prerequisites"></a>Krav

För att kunna fråga en offentlig app behöver du:

* Information om din språk understanding (LUIS) :
    * **Prognosnyckel** - som kan erhållas från [LUIS Portal](https://www.luis.ai/). Om du inte redan har en prenumeration för att skapa en nyckel kan du registrera dig för ett [gratis konto](https://azure.microsoft.com/free/).
    * **Underdomänen Förutsägelseslutpunkt** – underdomänen är också **namnet** på LUIS-resursen.
* Ett LUIS-app-ID – använd det offentliga `df67dcdb-c37d-46af-88e1-8b97951ca1c2`IoT-app-ID:et för . Användarfrågan som används i snabbstartskoden är specifik för den appen.

## <a name="use-the-browser-to-see-predictions"></a>Använd webbläsaren för att se förutsägelser

1. Öppna en webbläsare.
1. Använd de fullständiga webbadresserna `YOUR-KEY` nedan och ersätt med din egen LUIS Prediction-nyckel. Begäranden är GET-begäranden och inkluderar auktoriseringen, med luis-förutsägelsenyckeln, som en frågesträngparameter.

    #### <a name="v3-prediction-request"></a>[Begäran om V3-förutsägelse](#tab/V3-1-1)


    Formatet på V3-URL:en för en **GET-slutpunktsbegäran** (efter fack) är:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[Begäran om V2-förutsägelse](#tab/V2-1-2)

    Formatet på V2-URL:en för en **GET-slutpunktsbegäran** är:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
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
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
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
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
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

## <a name="next-steps"></a>Nästa steg

Läs mer om:
* [Slutpunkt för V3-förutsägelse](luis-migration-api-v3.md)
* [Anpassade underdomäner](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Skapa en app i LUIS-portalen](get-started-portal-build-app.md)
