---
title: Hämta modell med REST-anrop iC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: f4d180dd6ad99d5bc00e6970e22b756aa26275da
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76268261"
---
## <a name="prerequisites"></a>Krav

* Start nyckel.
* Importera [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) -appen från kognitiva-tjänsterna – språkförståelseing GitHub-lagringsplatsen.
* LUIS program-ID för den importerade TravelAgent-appen. Program-ID visas på programmets instrumentpanel.
* Versions-ID: t i programmet som tar emot yttranden. Standard-id:t är ”0.1”.
* [Python 3.6](https://www.python.org/downloads/) eller senare.
* [Visual Studio-kod](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>Hämta LUIS-nyckel

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]


## <a name="change-model-programmatically"></a>Ändra modell program mässigt

Använd Go för att lägga till en enhets- [API](https://aka.ms/luis-apim-v3-authoring) som har registrerats av enheten i programmet. 

1. Skapa en ny fil med namnet `model.py`. Lägg till följande kod:

    ```python
    ########### Python 3.6 #############
    import requests
    
    # Starter key
    LUIS_authoringKey  = "YOUR-KEY"
    
    LUIS_APP_ID = "YOUR-APP-ID"
    
    # Authoring endpoint, example: westus.api.cognitive.microsoft.com
    LUIS_ENDPOINT = "YOUR-ENDPOINT"

    # The version number of your LUIS app
    LUIS_APP_VERSION = "0.1"
    
    URI_AddUtterances = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/examples'
    URI_Train = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/train'
    
    HEADERS = {'Ocp-Apim-Subscription-Key': LUIS_authoringKey}
    
    def addUtterances():
        r = requests.post(URI_AddUtterances,headers=HEADERS)
        print(r.json())
    
    def train():
        r = requests.post(URI_Train,headers=HEADERS)
        print(r.json())
    
    def trainStatus():
        r = requests.get(URI_Train,headers=HEADERS)
        print(r.json())
    
    addUtterances()
    train()
    trainStatus()
    ```
1. Ersätt följande värden:

    * `YOUR-KEY` med din start nyckel
    * `YOUR-ENDPOINT` med slut punkten, till exempel `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` med appens ID

1. Med en kommando tolk i samma katalog som den plats där du skapade filen, anger du följande kommando för att köra filen:

    ```console
    python model.py
    ```  

## <a name="luis-keys"></a>LUIS-nycklar

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här snabb starten tar du bort filen från fil systemet. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metod tips för en app](../luis-concept-best-practices.md)
