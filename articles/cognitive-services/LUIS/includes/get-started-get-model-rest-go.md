---
title: Hämta modell med REST-anrop i go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: a153416a247ec3a38ec29e95b83fa919e765942b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966761"
---
## <a name="prerequisites"></a>Krav

* Azure Language Understanding-redigering av resurs 32-tangenten och URL: en för redigering av slut punkter. Skapa med [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) eller [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importera [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) -appen från kognitiva-tjänsterna – språkförståelseing GitHub-lagringsplatsen.
* LUIS program-ID för den importerade TravelAgent-appen. Program-ID visas på programmets instrumentpanel.
* Versions-ID: t i programmet som tar emot yttranden. Standard-id:t är ”0.1”.
* Programmeringsspråket [Go](https://golang.org/)
* [Visual Studio-kod](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Ändra modell program mässigt

Använd Go för att lägga till en enhets- [API](https://aka.ms/luis-apim-v3-authoring) som har registrerats av enheten i programmet.

1. Skapa en ny fil med namnet `predict.go`. Lägg till följande kod:

    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )

    // main function
    func main() {

        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"

        // NOTE: change to your authoring key
        var authoringKey = "YOUR-KEY"

        // NOTE: change to your authoring key's endpoint, for example, your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        var version = "0.1"

        var exampleUtterances = `
        [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ]
        `

        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)

        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)

        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }

    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)

        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){

        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)

        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){

        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){

        client := &http.Client{}

        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)

        fmt.Println("body")
        fmt.Println(body)

        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }
    ```

1. Ersätt värdena som börjar med `YOUR-` med dina egna värden.

    |Information|Syfte|
    |--|--|
    |`YOUR-KEY`|Din redigerings nyckel för 32-tecknen.|
    |`YOUR-ENDPOINT`| URL-slutpunkten för redigering. Till exempel `replace-with-your-resource-name.api.cognitive.microsoft.com`. Du anger resurs namnet när du skapade resursen.|
    |`YOUR-APP-ID`| Ditt LUIS app-ID. |

    Tilldelade nycklar och resurser visas i LUIS-portalen i avsnittet hantera på sidan **Azure-resurser** . App-ID: t är tillgängligt i samma hantera-avsnitt på sidan **program inställningar** .

1. Med en kommando tolk i samma katalog som den plats där du skapade filen, anger du följande kommando för att kompilera go-filen:

    ```console
    go build model.go
    ```

1. Kör Go-programmet från kommandoraden genom att ange följande text i kommandotolken:

    ```console
    go run model.go
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här snabb starten tar du bort filen från fil systemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metod tips för en app](../luis-concept-best-practices.md)