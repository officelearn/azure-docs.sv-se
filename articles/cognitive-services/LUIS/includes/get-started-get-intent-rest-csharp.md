---
title: 'Få förutsägelse med REST-anrop i C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 20916ff80ae52ee9fc215d87c0987900d89e590a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733266"
---
## <a name="prerequisites"></a>Krav

* [.NET Core V 2.2 +](https://dotnet.microsoft.com/download)
* [Visual Studio-koden](https://code.visualstudio.com/)
* Ett LUIS app-ID – Använd ID: t för `df67dcdb-c37d-46af-88e1-8b97951ca1c2`den offentliga IoT-appen. Den användar fråga som används i snabb starts koden är speciell för den appen.

## <a name="create-luis-runtime-key-for-predictions"></a>Skapa LUIS runtime Key för förutsägelser

1. Logga in på [Azure Portal](https://portal.azure.com)
1. Klicka på [skapa **language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Ange alla nödvändiga inställningar för körnings nyckel:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Plats|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0`– den minimala pris nivån|
    |Resursgrupp|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs sidan.
1. Samla in `endpoint` konfigurerade och `key`a.

## <a name="get-intent-programmatically"></a>Hämta avsikter programmatiskt

Använd C# (.NET Core) för att fråga [förutsägelse slut punkten](https://aka.ms/luis-apim-v3-prediction) och få ett förutsägelse resultat.

1. Skapa ett nytt konsol program som riktar sig mot språket C#, med ett projekt-och `predict-with-rest`mappnamn för.

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Ändra till den `predict-with-rest` katalog som du nyss skapade och installera nödvändiga beroenden med följande kommandon:

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. Öppna `Program.cs` i din favorit-IDE eller-redigerare. Skriv sedan över `Program.cs` med följande kod:

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
                // YOUR-KEY: 32 character key
                var key = "YOUR-KEY";

                // YOUR-ENDPOINT: example is your-resource-name.api.cognitive.microsoft.com
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

1. Ersätt värdena `YOUR-KEY` och `YOUR-ENDPOINT` med din egen förutsägelse nyckel och slut punkt.

    |Information|Syfte|
    |--|--|
    |`YOUR-KEY`|Din nyckel för att förutsäga 32-tecknen.|
    |`YOUR-ENDPOINT`| URL-slutpunkten för förutsägelse. Till exempel `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Bygg konsol programmet med det här kommandot:

    ```console
    dotnet build
    ```

1. Kör konsolprogrammet. Konsolens utdata visar samma JSON som du såg tidigare i webbläsarfönstret.

    ```console
    dotnet run
    ```

1. Granska det förutsägelse svar som returneras som JSON:

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

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här snabb starten tar du bort filen från fil systemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till yttranden och träna](../get-started-get-model-rest-apis.md)