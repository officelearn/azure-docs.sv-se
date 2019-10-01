---
title: 'Snabbstart: Få avsikt med webb läsar LUIS'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten använder du en tillgänglig offentlig LUIS-app för att fastställa en användares avsikt från konversations text i en webbläsare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703134"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Snabbstart: Skapa avsikt med en webbläsare

Om du vill förstå vad som returneras från en LUIS-slutpunkt för förutsägelser kan du granska resultatet i en webbläsare. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna skicka frågor till en offentlig app:

* Din egen Language Understanding-nyckel (LUIS). Om du inte redan har en prenumeration för att skapa en nyckel kan du registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Den offentliga appens ID: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Använd webbläsaren för att se förutsägelser

1. Öppna en webbläsare. 
1. Använd de fullständiga URL: erna nedan och ersätt `{your-key}` med din egen LUIS-nyckel. Begär Anden är GET-begäranden och inkluderar auktoriseringen med din LUIS-nyckel som en frågesträngparametern.

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2-begäran om slut punkts förutsägelse](#tab/V2)
    
    Formatet på v2-URL: en för en begäran om att **Hämta** slut punkter är:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 förutsägelse slut punkts förfrågan](#tab/V3)
    
    
    Formatet på v3-URL: en för en begäran om **hämtning** av slut punkt (per fack) är:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. Klistra in URL:en i ett webbläsarfönster och tryck på Retur. Du ser ett JSON-resultat i webbläsaren som indikerar att LUIS har identifierat avsikten `HomeAutomation.TurnOn` som troligaste avsikt och entiteten `HomeAutomation.Operation` med värdet `on`.

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

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

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
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

    * * *

1. Om du vill se alla avsikter lägger du till lämplig frågesträngparametern. 

    #### <a name="v2-prediction-endpointtabv2"></a>[V2-förutsägelse slut punkt](#tab/V2)

    Lägg till `verbose=true` till slutet av QueryString för att **Visa alla avsikter**:

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

    #### <a name="v3-prediction-endpointtabv3"></a>[V3 förutsägelse slut punkt](#tab/V3)

    Lägg till `show-all-intents=true` till slutet av QueryString för att **Visa alla avsikter**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
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

    * * * 

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Skapa en app i LUIS-portalen](get-started-portal-build-app.md)