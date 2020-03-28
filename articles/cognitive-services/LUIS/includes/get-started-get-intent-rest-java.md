---
title: Få uppsåt med REST-anrop i Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 1bd7a2bb6d3393aca397686a2817f1dcd5f89a38
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987818"
---
## <a name="prerequisites"></a>Krav

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio-kod](https://code.visualstudio.com/) eller din favorit IDE
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

## <a name="get-intent-programmatically"></a>Hämta avsikter programmatiskt

Använd Java för att fråga [förutsägelseslutpunkten](https://aka.ms/luis-apim-v3-prediction) och få ett förutsägelseresultat.

1. Gör en underkatalog `lib` namngiven och kopiera i följande java libs:

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Kopiera följande kod för att skapa en klass i en fil som heter `Predict.java`:

    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;

    public class Predict {

        public static void main(String[] args)
        {
            HttpClient httpclient = HttpClients.createDefault();

            try
            {

                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                // Add your prediction Runtime key
                String Key = "YOUR-KEY";

                // Add your endpoint, example is your-resource-name.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";

                String Utterance = "turn on all lights";

                // Begin endpoint URL string building
                URIBuilder endpointURLbuilder = new URIBuilder("https://" + Endpoint + "/luis/prediction/v3.0/apps/" + AppId + "/slots/production/predict?");

                // query string params
                endpointURLbuilder.setParameter("query", Utterance);
                endpointURLbuilder.setParameter("subscription-key", Key);
                endpointURLbuilder.setParameter("show-all-intents", "true");
                endpointURLbuilder.setParameter("verbose", "true");

                // create URL from string
                URI endpointURL = endpointURLbuilder.build();

                // create HTTP object from URL
                HttpGet request = new HttpGet(endpointURL);

                // access LUIS endpoint - analyze text
                HttpResponse response = httpclient.execute(request);

                // get response
                HttpEntity entity = response.getEntity();


                if (entity != null)
                {
                    System.out.println(EntityUtils.toString(entity));
                }
            }

            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        }
    }
    ```

1. Ersätt `YOUR-KEY` värdena och `YOUR-ENDPOINT` med din egen förutsägelse **körningsnyckel** och slutpunkt.

    |Information|Syfte|
    |--|--|
    |`YOUR-KEY`|Din 32 tecken förutsägelse **Runtime** nyckel.|
    |`YOUR-ENDPOINT`| Slutpunkten för förutsägelse-URL. Till exempel `replace-with-your-resource-name.api.cognitive.microsoft.com`.|


1. Kompilera java-programmet från kommandoraden:

    ```console
    javac -cp ":lib/*" Predict.java
    ```

1. Kör java-programmet från kommandoraden:

    ```console
    java -cp ":lib/*" Predict
    ```

1. Granska förutsägelsesvaret, som returneras som JSON:

    ```console
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

När du är klar med den här snabbstarten tar du bort filen från filsystemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till yttranden och träna med Java](../get-started-get-model-rest-apis.md)