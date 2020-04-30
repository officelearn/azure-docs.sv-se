---
title: Kom avsikt med REST-anrop i farten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: d9af9f788e2309cdf687e0a13b77220c150a0e1e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733334"
---
## <a name="prerequisites"></a>Krav

* [Go](https://golang.org/) -programmeringsspråk
* [Visual Studio-koden](https://code.visualstudio.com/)
* Ett LUIS app-ID – Använd ID: t för `df67dcdb-c37d-46af-88e1-8b97951ca1c2`den offentliga IoT-appen. Den användar fråga som används i snabb starts koden är speciell för den appen.

## <a name="create-luis-runtime-key-for-predictions"></a>Skapa LUIS runtime Key för förutsägelser

1. Logga in på [Azure Portal](https://portal.azure.com)
1. Klicka på [skapa **language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Ange alla nödvändiga inställningar för **körnings** nyckel:

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

Använd Go för att fråga efter [förutsägelse slut punkten](https://aka.ms/luis-apim-v3-prediction) och få ett förutsägelse resultat.

1. Skapa en ny fil med namnet `predict.go`. Lägg till följande kod:

    ```go
    package main

    /* Do dependencies */
    import (
        "fmt"
        "net/http"
        "net/url"
        "io/ioutil"
        "log"
    )
    func main() {

        // public app
        var appID = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"

        // utterance for public app
        var utterance = "turn on all lights"

        // YOUR-KEY - your **Runtime** key
        var endpointKey = "YOUR-KEY"

        // YOUR-ENDPOINT - example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        endpointPrediction(appID, endpointKey, endpoint, utterance)
    }
    func endpointPrediction(appID string, endpointKey string, endpoint string, utterance string) {

        var endpointUrl = fmt.Sprintf("https://%s/luis/prediction/v3.0/apps/%s/slots/production/predict?subscription-key=%s&verbose=true&show-all-intents=true&query=%s", endpoint, appID, endpointKey, url.QueryEscape(utterance))

        response, err := http.Get(endpointUrl)

        if err!=nil {
            // handle error
            fmt.Println("error from Get")
            log.Fatal(err)
        }

        response2, err2 := ioutil.ReadAll(response.Body)

        if err2!=nil {
            // handle error
            fmt.Println("error from ReadAll")
            log.Fatal(err2)
        }

        fmt.Println("response")
        fmt.Println(string(response2))
    }
    ```

1. Ersätt värdena `YOUR-KEY` och `YOUR-ENDPOINT` med din egen **körnings** nyckel och slut punkt för förutsägelse.

    |Information|Syfte|
    |--|--|
    |`YOUR-KEY`|Din 32-teckenuppsättning med **körnings** nyckel.|
    |`YOUR-ENDPOINT`| URL-slutpunkten för förutsägelse. Till exempel `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Med en kommando tolk i samma katalog som den plats där du skapade filen, anger du följande kommando för att kompilera go-filen:

    ```console
    go build predict.go
    ```

1. Kör Go-programmet från kommandoraden genom att ange följande text i kommandotolken:

    ```console
    go run predict.go
    ```

    Svaret från kommandotolken är:

    ```console
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value a7b206911f714e71a1ddae36928a61cc
    endpoint has value westus2.api.cognitive.microsoft.com
    utterance has value turn on all lights
    response
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    JSON-formaterad för läsbarhet:

    ```json
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