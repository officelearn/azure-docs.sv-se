---
title: 'Snabb start: skapa avsikt med webb läsar LUIS'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten använder du en tillgänglig offentlig LUIS-app för att fastställa en användares avsikt från konversations text i en webbläsare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: 2ca0f9faf1623df9212072abbc960cba41a6414b
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165745"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Snabb start: skapa avsikt med en webbläsare

Om du vill förstå vad som returneras från en LUIS-slutpunkt för förutsägelser kan du granska resultatet i en webbläsare. 

## <a name="prerequisites"></a>Krav

Du behöver följande för att kunna skicka frågor till en offentlig app:

* Din egen Language Understanding (LUIS) redigerings-eller förutsägelse nyckel som kan hämtas från [Luis portal (för hands version)](https://preview.luis.ai/). Om du inte redan har en prenumeration för att skapa en nyckel kan du registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/). 
* Den offentliga appens ID: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Använd webbläsaren för att se förutsägelser

1. Öppna en webbläsare. 
1. Använd de fullständiga URL: erna nedan och ersätt `YOUR-KEY` med din egen LUIS-redigering eller förutsägelse nyckel. Begär Anden är GET-begäranden och inkluderar auktoriseringen, med din LUIS-redigerings-eller förutsägelse nyckel, som en frågesträngparametern.

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[V3 förutsägelse förfrågan](#tab/V3-1-1)
    
    
    Formatet på v3-URL: en för en begäran om **hämtning** av slut punkt (per fack) är:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[V2 förutsägelse förfrågan](#tab/V2-1-2)
    
    Formatet på v2-URL: en för en begäran om att **Hämta** slut punkter är:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Klistra in URL:en i ett webbläsarfönster och tryck på Retur. Du ser ett JSON-resultat i webbläsaren som indikerar att LUIS har identifierat avsikten `HomeAutomation.TurnOn` som troligaste avsikt och entiteten `HomeAutomation.Operation` med värdet `on`.

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[V3 förutsägelse svar](#tab/V3-2-1)

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

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[V2 förutsägelse svar](#tab/V2-2-2)

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

1. Om du vill se alla avsikter lägger du till lämplig frågesträngparametern. 

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[V3 förutsägelse slut punkt](#tab/V3-3-1)

    Lägg till `show-all-intents=true` i slutet av QueryString för att **Visa alla avsikter**:

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

    #### <a name="v2-prediction-endpointtabv2"></a>[V2-förutsägelse slut punkt](#tab/V2)

    Lägg till `verbose=true` i slutet av QueryString för att **Visa alla avsikter**:

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

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Skapa en app i LUIS-portalen](get-started-portal-build-app.md)
