---
title: Hämta modell med REST-anrop i Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: 7d1915025c31a0c201da87b1d4bde9b52659e5e3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655532"
---
## <a name="prerequisites"></a>Krav

* Azure Language Understanding-redigering av resurs 32-tangenten och URL: en för redigering av slut punkter. Skapa med [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) eller [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importera [pizza](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json) -appen från `Azure-Samples/cognitive-services-sample-data-files` GitHub-lagringsplatsen.
* LUIS program-ID för den importerade pizza-appen. Program-ID visas på programmets instrumentpanel.
* Den versions-ID i programmet som tar emot yttrandena.
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) eller din favorit-IDE

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Ändra modell program mässigt

1. Skapa en ny mapp för att lagra ditt Java-projekt, till exempel `java-model-with-rest` .

1. Skapa en under katalog med namnet `lib` och kopiera i följande Java-libs till under `lib` katalogen:

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Skapa en ny fil med namnet `Model.java`. Lägg till följande kod:

```java
import java.io.*;
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

// To compile, execute this command at the console:
//      Windows: javac -cp ";lib/*" Model.java
//      macOs: javac -cp ":lib/*" Model.java
//      Linux: javac -cp ":lib/*" Model.java

// To run, execute this command at the console:
//      Windows: java -cp ";lib/*" Model
//      macOs: java -cp ":lib/*" Model
//      Linux: java -cp ":lib/*" Model

public class Model
{
    public static void main(String[] args)
    {
        try
        {
            //////////
            // Values to modify.

            // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
            String AppId = "YOUR-APP-ID";

            // YOUR-AUTHORING-KEY: Your LUIS authoring key, 32 character value.
            String Key = "YOUR-AUTHORING-KEY";

            // YOUR-AUTHORING-ENDPOINT: Replace this with your authoring key endpoint.
            // For example, "https://your-resource-name.api.cognitive.microsoft.com/"
            String Endpoint = "YOUR-AUTHORING-ENDPOINT";

            // NOTE: Replace this your version number. The Pizza app uses a version number of "0.1".
            String Version = "0.1";
            //////////

            // The list of utterances to add, in JSON format.
            String Utterances = "[{'text': 'order a pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 12}]}, {'text': 'order a large pepperoni pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'PizzaType', 'startCharIndex': 14, 'endCharIndex': 28}, {'entityName': 'Size', 'startCharIndex': 8, 'endCharIndex': 12}]}, {'text': 'I want two large pepperoni pizzas on thin crust', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'PizzaType', 'startCharIndex': 17, 'endCharIndex': 32}, {'entityName': 'Size', 'startCharIndex': 11, 'endCharIndex': 15}, {'entityName': 'Quantity', 'startCharIndex': 7, 'endCharIndex': 9}, {'entityName': 'Crust', 'startCharIndex': 37, 'endCharIndex': 46}]}]";

            // Create the URLs for uploading example utterances and for training.
            URIBuilder addUtteranceURL = new URIBuilder(Endpoint + "luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/examples");
            URIBuilder trainURL = new URIBuilder(Endpoint + "luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/train");
            URI addUtterancesURI = addUtteranceURL.build();
            URI trainURI = trainURL.build();


            // Add the utterances.

            // Create the request.
            HttpClient addUtterancesClient = HttpClients.createDefault();
            HttpPost addUtterancesRequest = new HttpPost(addUtterancesURI);

            // Add the headers.
            addUtterancesRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
            addUtterancesRequest.setHeader("Content-type","application/json");

            // Add the body.
            StringEntity stringEntity = new StringEntity(Utterances);
            addUtterancesRequest.setEntity(stringEntity);

            // Execute the request and obtain the response.
            HttpResponse addUtterancesResponse = addUtterancesClient.execute(addUtterancesRequest);
            HttpEntity addUtterancesEntity = addUtterancesResponse.getEntity();

            // Print the response on the console.
            if (addUtterancesEntity != null)
            {
                System.out.println(EntityUtils.toString(addUtterancesEntity));
            }


            // Train the model.

            // Create the request.
            HttpClient trainClient = HttpClients.createDefault();
            HttpPost trainRequest = new HttpPost(trainURI);

            // Add the headers.
            trainRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
            trainRequest.setHeader("Content-type","application/json");

            // Execute the request and obtain the response.
            HttpResponse trainResponse = trainClient.execute(trainRequest);
            HttpEntity trainEntity = trainResponse.getEntity();

            // Print the response on the console.
            if (trainEntity != null)
            {
                System.out.println(EntityUtils.toString(trainEntity));
            }


            // Get the training status.


            // Create the request.
            HttpClient trainStatusClient = HttpClients.createDefault();
            HttpGet trainStatusRequest = new HttpGet(trainURI);

            // Add the headers.
            trainStatusRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
            trainStatusRequest.setHeader("Content-type","application/json");

            // Execute the request and obtain the response.
            HttpResponse trainStatusResponse = trainStatusClient.execute(trainStatusRequest);
            HttpEntity trainStatusEntity = trainStatusResponse.getEntity();

            // Print the response on the console.
            if (trainStatusEntity != null)
            {
                System.out.println(EntityUtils.toString(trainStatusEntity));
            }
        }

        // Display errors if they occur.
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
```

1. Ersätt värdena som börjar med `YOUR-` med dina egna värden.

    |Information|Syfte|
    |--|--|
    |`YOUR-APP-ID`| Ditt LUIS app-ID. |
    |`YOUR-AUTHORING-KEY`|Din redigerings nyckel för 32-tecknen.|
    |`YOUR-AUTHORING-ENDPOINT`| URL-slutpunkten för redigering. Till exempel `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Du anger resurs namnet när du skapade resursen.|

    Tilldelade nycklar och resurser visas i LUIS-portalen i avsnittet hantera på sidan **Azure-resurser** . App-ID: t är tillgängligt i samma hantera-avsnitt på sidan **program inställningar** .

1. Med en kommando tolk i samma katalog som den plats där du skapade `Model.java` filen, anger du följande kommando för att kompilera Java-filen:

    ::: zone pivot="client-operating-system-linux"

    ```console
    javac -cp ":lib/*" Model.java
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-macos"

    ```console
    javac -cp ":lib/*" Model.java
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-windows"

    ```console
    javac -cp ";lib/*" Model.java
    ```

    ::: zone-end

1. Kör Java-programmet från kommando raden genom att ange följande text i kommando tolken:

    ::: zone pivot="client-operating-system-linux"

    ```console
    java -cp ":lib/*" Model
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-macos"

    ```console
    java -cp ":lib/*" Model
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-windows"

    ```console
    java -cp ";lib/*" Model
    ```

    ::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här snabb starten tar du bort projektmappen från fil systemet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metod tips för en app](../luis-concept-best-practices.md)