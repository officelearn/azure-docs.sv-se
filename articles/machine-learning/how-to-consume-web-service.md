---
title: Skapa klient för modell som distribueras som webbtjänst
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder en webbtjänst som genererades när en modell distribuerades med Azure Machine Learning-modellen. Webbtjänsten exponerar ett REST API. Skapa klienter för det här API:et med hjälp av det programmeringsspråk du väljer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: a86b8ddb59719db9bdaffea44aecd5428ad16834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282672"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Använda en Azure Machine Learning-modell som distribueras som en webbtjänst
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Distribuera en Azure Machine Learning-modell som en webbtjänst skapar ett REST API. Du kan skicka data till det här API:et och ta emot förutsägelsen som returneras av modellen. I det här dokumentet får du lära dig hur du skapar klienter för webbtjänsten med hjälp av C#, Go, Java och Python.

Du skapar en webbtjänst när du distribuerar en avbildning till Azure Container Instances, Azure Kubernetes Service eller fältprogrammerbara gate arrayer (FPGA). Du skapar bilder från registrerade modeller och poängfiler. Du hämtar URI som används för att komma åt en webbtjänst med hjälp av [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Om autentisering är aktiverat kan du också använda SDK för att hämta autentiseringsnycklarna eller token.

Det allmänna arbetsflödet för att skapa en klient som använder en datorinlärningswebbtjänst är:

1. Använd SDK för att hämta anslutningsinformationen.
1. Bestäm vilken typ av begärandedata som används av modellen.
1. Skapa ett program som anropar webbtjänsten.

> [!TIP]
> Exemplen i det här dokumentet skapas manuellt utan användning av OpenAPI-specifikationer (Swagger). Om du har aktiverat en OpenAPI-specifikation för distributionen kan du använda verktyg som [swagger-codegen](https://github.com/swagger-api/swagger-codegen) för att skapa klientbibliotek för tjänsten.

## <a name="connection-information"></a>Anslutningsinformation

> [!NOTE]
> Använd Azure Machine Learning SDK för att hämta webbtjänstinformation. Det här är en Python SDK. Du kan använda vilket språk som helst för att skapa en klient för tjänsten.

Klassen [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) innehåller den information du behöver för att skapa en klient. Följande `Webservice` egenskaper är användbara när du vill skapa ett klientprogram:

* `auth_enabled`- Om nyckelautentisering är `True`aktiverat, ; annars. `False`
* `token_auth_enabled`- Om tokenautentisering är `True`aktiverad; annars. `False`
* `scoring_uri`- REST API-adressen.
* `swagger_uri`- Adressen till OpenAPI-specifikationen. Den här URI:n är tillgänglig om du har aktiverat automatisk schemagenerering. Mer information finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

Det finns tre sätt att hämta den här informationen för distribuerade webbtjänster:

* När du distribuerar `Webservice` en modell returneras ett objekt med information om tjänsten:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Du kan `Webservice.list` använda för att hämta en lista över distribuerade webbtjänster för modeller på arbetsytan. Du kan lägga till filter för att begränsa listan med returnerad information. Mer information om vad som kan filtreras på finns i referensdokumentationen [för Webservice.list.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py)

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Om du känner till namnet på den distribuerade `Webservice`tjänsten kan du skapa en ny instans av och ange arbetsytan och tjänstnamnet som parametrar. Det nya objektet innehåller information om den distribuerade tjänsten.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>Skyddad webbtjänst

Om du har säkrat den distribuerade webbtjänsten med hjälp av ett TLS/SSL-certifikat kan du använda [HTTPS](https://en.wikipedia.org/wiki/HTTPS) för att ansluta till tjänsten med bedömnings- eller skryt-URI. HTTPS hjälper till att skydda kommunikationen mellan en klient och en webbtjänst genom att kryptera kommunikation mellan de två. Kryptering använder [TLS (Transport Layer Security).](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS kallas ibland fortfarande *SSL (Secure Sockets Layer),* som var föregångaren till TLS.

> [!IMPORTANT]
> Webbtjänster som distribueras av Azure Machine Learning stöder endast TLS version 1.2. När du skapar ett klientprogram kontrollerar du att det stöder den här versionen.

Mer information finns i [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Autentisering för tjänster

Azure Machine Learning innehåller två sätt att styra åtkomsten till dina webbtjänster.

|Autentiseringsmetod|Aci|AKS|
|---|---|---|
|Nyckel|Inaktiverad som standard| Aktiverat som standard|
|Token| Inte tillgänglig| Inaktiverad som standard |

När du skickar en begäran till en tjänst som är skyddad med en nyckel eller token använder du __auktoriseringshuvudet__ för att skicka nyckeln eller token. Nyckeln eller token måste formateras `Bearer <key-or-token>`som `<key-or-token>` , var är din nyckel eller token värde.

#### <a name="authentication-with-keys"></a>Autentisering med nycklar

När du aktiverar autentisering för en distribution skapar du automatiskt autentiseringsnycklar.

* Autentisering är aktiverat som standard när du distribuerar till Azure Kubernetes Service.
* Autentisering är inaktiverat som standard när du distribuerar till Azure Container Instances.

Om du vill `auth_enabled` styra autentiseringen använder du parametern när du skapar eller uppdaterar en distribution.

Om autentisering är aktiverat kan `get_keys` du använda metoden för att hämta en primär och sekundär autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du behöver återskapa en [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)nyckel använder du .

#### <a name="authentication-with-tokens"></a>Autentisering med token

När du aktiverar tokenautentisering för en webbtjänst måste en användare tillhandahålla en Azure Machine Learning JWT-token till webbtjänsten för att komma åt den. 

* Tokenautentisering inaktiveras som standard när du distribuerar till Azure Kubernetes Service.
* Tokenautentisering stöds inte när du distribuerar till Azure Container Instances.

Om du vill styra `token_auth_enabled` tokenautentisering använder du parametern när du skapar eller uppdaterar en distribution.

Om tokenautentisering är aktiverat kan `get_token` du använda metoden för att hämta en innehavartoken och att tokens förfallotid:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter `refresh_by` tokens tid. 

## <a name="request-data"></a>Begär data

REST API förväntar sig att brödtexten för begäran ska vara ett JSON-dokument med följande struktur:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Datastrukturen måste matcha vad bedömningsskriptet och modellen i tjänsten förväntar sig. Bedömningsskriptet kan ändra data innan det går till modellen.

Modellen i exemplet [Tåg i anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) förväntar sig till exempel en matris med 10 nummer. Bedömningsskriptet för det här exemplet skapar en Numpy-matris från begäran och skickar den till modellen. I följande exempel visas de data som den här tjänsten förväntar sig:

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
```

Webbtjänsten kan acceptera flera uppsättningar data i en begäran. Det returnerar ett JSON-dokument som innehåller en matris med svar.

### <a name="binary-data"></a>Binära data

Information om hur du aktiverar stöd för binära data i tjänsten finns i [Binära data](how-to-deploy-and-where.md#binary).

### <a name="cross-origin-resource-sharing-cors"></a>Gemensamt för resursdelning (CORS)

Information om hur du aktiverar CORS-support i din tjänst finns i [Resursdelning över flera källor](how-to-deploy-and-where.md#cors).

## <a name="call-the-service-c"></a>Ring tjänsten (C#)

Det här exemplet visar hur du använder C# för att anropa webbtjänsten som skapats från exemplet [Tåg i anteckningsbok:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

De resultat som returneras liknar följande JSON-dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Ring tjänsten (Gå)

Det här exemplet visar hur du använder Gå för att anropa webbtjänsten som skapats från exemplet [Tåg i anteckningsbok:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

De resultat som returneras liknar följande JSON-dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Ring tjänsten (Java)

Det här exemplet visar hur du använder Java för att anropa webbtjänsten som skapats från exemplet [Tåg i anteckningsbok:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

De resultat som returneras liknar följande JSON-dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Anropa tjänsten (Python)

Det här exemplet visar hur du använder Python för att anropa webbtjänsten som skapats från exemplet [Tåg i anteckningsbok:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

De resultat som returneras liknar följande JSON-dokument:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Använda tjänsten från Power BI

Power BI stöder förbrukning av Azure Machine Learning-webbtjänster för att berika data i Power BI med förutsägelser. 

Om du vill generera en webbtjänst som stöds för förbrukning i Power BI måste schemat ha stöd för det format som krävs av Power BI. [Lär dig hur du skapar ett Power BI-stödda schema](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script).

När webbtjänsten har distribuerats kan den användas från Power BI-dataflöden. [Lär dig hur du använder en Azure Machine Learning-webbtjänst från Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Nästa steg

Om du vill visa en referensarkitektur för realtidsbedömning av Python- och djupinlärningsmodeller går du till [Azure Architecture Center](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
