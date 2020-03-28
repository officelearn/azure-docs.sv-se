---
title: Få uppsåt med REST-anrop i Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 23ac98f91c989c9bedb6b91e6a7ce26dc164ac5a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987815"
---
## <a name="prerequisites"></a>Krav

* [Python 3.6](https://www.python.org/downloads/) eller senare.
* [Visual Studio-kod](https://code.visualstudio.com/)
* Offentligt app-ID:`df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>Skapa LUIS-körningsnyckel för förutsägelser

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Klicka på [Skapa **språk understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Ange alla nödvändiga **Runtime** inställningar för Runtime-tangenten:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Location|Välj en plats i närheten och tillgänglig|
    |Prisnivå|`F0`- den minimala prisnivån|
    |Resursgrupp|Välj en tillgänglig resursgrupp|

1. Klicka på **Skapa** och vänta tills resursen har skapats. När den har skapats navigerar du till resurssidan.
1. Samla konfigurerade `endpoint` `key`och en .

## <a name="get-intent-from-the-prediction-endpoint"></a>Hämta avsikt från slutpunkten för förutsägelse

Använd Python för att fråga [förutsägelseslutpunkten](https://aka.ms/luis-apim-v3-prediction) och få ett förutsägelseresultat.

1. Kopiera kodavsnittet till en `predict.py`fil som heter :

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

1. Ersätt `YOUR-KEY` värdena och `YOUR-ENDPOINT` med din egen förutsägelse **körningsnyckel** och slutpunkt.

    |Information|Syfte|
    |--|--|
    |`YOUR-KEY`|Din 32 tecken förutsägelse **Runtime** nyckel.|
    |`YOUR-ENDPOINT`| Slutpunkten för förutsägelse-URL. Till exempel `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Installera `requests` beroendet. Detta används för att göra HTTP-begäranden:

    ```console
    pip install requests
    ```

1. Kör skriptet med det här konsolkommandot:

    ```console
    python predict.py
    ```

1. Granska förutsägelsesvaret, som returneras som JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Här är JSON svar formaterad för läsbarhet:

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

När du är klar med den här snabbstarten tar du bort filen från filsystemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till yttranden och träna](../get-started-get-model-rest-apis.md)