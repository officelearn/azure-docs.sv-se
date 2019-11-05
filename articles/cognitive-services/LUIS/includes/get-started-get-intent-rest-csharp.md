---
title: Få förutsägelse med REST-anrop iC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: 81c95dc58e8cfaddf981e3911e88310cea508115
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499694"
---
## <a name="prerequisites"></a>Förutsättningar

* [.NET Core V 2.2 +](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* Offentlig app-ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2

## <a name="get-luis-key"></a>Hämta LUIS-nyckel

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>Hämta avsikter programmatiskt

Används C# för att fråga förutsägelse slut punkt Hämta [API](https://aka.ms/luis-apim-v3-prediction) för att hämta förutsägelse resultatet. 

1. Skapa ett nytt konsol program som riktar C# sig mot språket, med ett projekt-och mappnamn med `predict-with-rest`. 

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Installera nödvändiga beroenden med följande dotNet CLI-kommandon.

    ```console
    dotnet add package System.Net.Http
    ```
1. Skriv över Program.cs med följande kod:
    
   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    
    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: for example, the starter key
                var key = "YOUR-KEY";
                
                // YOUR-ENDPOINT: example is westus2.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"; 
    
                var utterance = "turn on all lights";
    
                MakeRequest(key, endpoint, appId, utterance);
    
                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);
    
                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
    
                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;
    
                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";
    
                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);
    
                var response = await client.GetAsync(endpointUri);
    
                var strResponseContent = await response.Content.ReadAsStringAsync();
                
                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. Ersätt följande värden:

    * `YOUR-KEY` med din start nyckel
    * `YOUR-ENDPOINT` med slut punkten, till exempel `westus2.api.cognitive.microsoft.com`

1. Skapa konsolprogrammet. 

    ```console
    dotnet build
    ```

1. Kör konsolprogrammet. Konsolens utdata visar samma JSON som du såg tidigare i webbläsarfönstret.

    ```console
    dotnet run
    ```

1. Granska förutsägelse svar i JSON-format:

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    JSON-svaret formaterat för läsbarhet: 

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

## <a name="luis-keys"></a>LUIS-nycklar

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här snabb starten tar du bort filen från fil systemet. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till yttranden och träna](../luis-get-started-cs-add-utterance.md)