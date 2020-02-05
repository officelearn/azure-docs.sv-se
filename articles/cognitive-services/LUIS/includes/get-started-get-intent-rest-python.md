---
title: Kom avsikt med REST-anrop i python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 23ac98f91c989c9bedb6b91e6a7ce26dc164ac5a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987815"
---
## <a name="prerequisites"></a>Krav

* [Python 3.6](https://www.python.org/downloads/) eller senare.
* [Visual Studio-kod](https://code.visualstudio.com/)
* ID för offentlig app: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>Skapa LUIS runtime Key för förutsägelser

1. Logga in på [Azure Portal](https://portal.azure.com)
1. Klicka på [skapa **language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Ange alla nödvändiga inställningar för **körnings** nyckel:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Location|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0` – den minimala pris nivån|
    |Resursgrupp|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs sidan.
1. Samla in konfigurerade `endpoint` och en `key`.

## <a name="get-intent-from-the-prediction-endpoint"></a>Hämta avsikt från förutsägelse slut punkten

Använd python för att fråga efter [förutsägelse slut punkten](https://aka.ms/luis-apim-v3-prediction) och få ett förutsägelse resultat.

1. Kopiera det här kodfragmentet till en fil med namnet `predict.py`:

    ```python
    ########### Python 3.6 #############
    import requests

    try:

        key = 'YOUR-KEY' # your Runtime key
        endpoint = 'YOUR-ENDPOINT' # such as 'your-resource-name.api.cognitive.microsoft.com'
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'

        headers = {
        }

        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }

        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())

    except Exception as e:
        print(f'{e}')
    ```

1. Ersätt `YOUR-KEY` och `YOUR-ENDPOINT` värden med din egen förutsägelse **körnings** nyckel och slut punkt.

    |Information|Syfte|
    |--|--|
    |`YOUR-KEY`|Din 32-teckenuppsättning med **körnings** nyckel.|
    |`YOUR-ENDPOINT`| URL-slutpunkten för förutsägelse. Till exempel `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Installera `requests` beroendet. Detta används för att göra HTTP-förfrågningar:

    ```console
    pip install requests
    ```

1. Kör skriptet med följande konsol kommando:

    ```console
    python predict.py
    ```

1. Granska det förutsägelse svar som returneras som JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Här är JSON-svaret formaterat för läsbarhet:

    ```JSON
    {
        "query": "turn on all lights",
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
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
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

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här snabb starten tar du bort filen från fil systemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till yttranden och träna](../get-started-get-model-rest-apis.md)