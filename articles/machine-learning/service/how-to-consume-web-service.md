---
title: Skapa en klient för att använda distribuerad webb tjänst
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder en webb tjänst som genererades när en modell distribuerades med Azure Machine Learning modell. Webb tjänsten exponerar en REST API. Skapa klienter för detta API genom att använda valfritt programmeringsspråk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 873f45a6cce85669581037c4c398a52b1ebd6d68
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966843"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Använd en Azure Machine Learning-modell som distribueras som en webbtjänst

Distribuera en Azure Machine Learning-modell som en webbtjänst skapas ett REST-API. Du kan skicka data till den här API: et och få förutsägelser som returneras av modellen. I det här dokumentet lär du dig hur du skapar klienter för webb tjänsten med C#hjälp av, go, Java och python.

Du skapar en webb tjänst när du distribuerar en avbildning till Azure Container Instances, Azure Kubernetes service eller Field-programmerbara grind mat ris (FPGA). Du skapar bilder från registrerade modeller och poängsättnings-filer. Du hämtar den URI som används för att få åtkomst till en webb tjänst med hjälp av [Azure Machine Learning SDK](https://aka.ms/aml-sdk). Du kan också använda SDK: N för att hämta autentiseringsnycklarna om autentisering är aktiverad.

Det allmänna arbets flödet för att skapa en klient som använder en Machine Learning-webbtjänst är:

1. Hämta anslutnings informationen med hjälp av SDK.
1. Bestäm vilken typ av begär ande data som används av modellen.
1. Skapa ett program som anropar webb tjänsten.

> [!TIP]
> Exemplen i det här dokumentet skapas manuellt utan användning av OpenAPI-specifikationer (Swagger). Om du har aktiverat en OpenAPI-specifikation för distributionen kan du använda verktyg som [Swagger-CODEGEN](https://github.com/swagger-api/swagger-codegen) för att skapa klient bibliotek för din tjänst.

## <a name="connection-information"></a>Anslutningsinformation

> [!NOTE]
> Använd Azure Machine Learning SDK för att hämta information om webb tjänsten. Det här är en Python-SDK. Du kan använda valfritt språk för att skapa en klient för tjänsten.

[Azureml. Core. WebService-](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) klassen innehåller den information du behöver för att skapa en-klient. Följande `Webservice` egenskaper är användbara när du skapar ett klient program:

* `auth_enabled`– Om Key Authentication har Aktiver ATS `True`, `False`annars,.
* `token_auth_enabled`– Om token-autentisering är `True`aktiverat, annars `False`,.
* `scoring_uri` – REST API-adress.


Det finns tre sätt att hämta den här informationen för distribuerade webbtjänster:

* När du distribuerar en modell, en `Webservice` objekt returneras med information om tjänsten:

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    ```

* Du kan använda `Webservice.list` att hämta en lista över distribuerade webbtjänster för modeller i din arbetsyta. Du kan lägga till filter för att begränsa listan med information som returneras. Mer information om vad som kan filtreras finns i referens dokumentationen för [WebService. list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) .

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    ```

* Om du känner till namnet på den distribuerade tjänsten kan du skapa en ny instans av `Webservice`och ange arbets ytan och tjänstens namn som parametrar. Det nya objektet innehåller information om den distribuerade tjänsten.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    ```

### <a name="authentication-for-services"></a>Autentisering för tjänster

Azure Machine Learning ger dig möjlighet att styra åtkomsten till dina webb tjänster på två sätt. 

|Autentiseringsmetod|ACI|AKS|
|---|---|---|
|Nyckel|Inaktiverat som standard| Aktive rad som standard|
|Token| Ej tillgänglig| Inaktiverat som standard |

#### <a name="authentication-with-keys"></a>Autentisering med nycklar

När du aktiverar autentisering för en distribution skapar du automatiskt nycklar för autentisering.

* Autentisering aktive ras som standard när du distribuerar till Azure Kubernetes-tjänsten.
* Autentisering inaktive ras som standard när du distribuerar till Azure Container Instances.

Om du vill kontrol lera autentiseringen använder `auth_enabled` du parametern när du skapar eller uppdaterar en distribution.

Om autentisering har aktiverats, kan du använda den `get_keys` metod för att hämta en primära och sekundära autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du vill återskapa en nyckel kan du använda [ `service.regen_key` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Autentisering med token

När du aktiverar token-autentisering för en webb tjänst måste en användare ange en Azure Machine Learning JWT-token för webb tjänsten för att få åtkomst till den. 

* Token-autentisering inaktive ras som standard när du distribuerar till Azure Kubernetes-tjänsten.
* Token-autentisering stöds inte när du distribuerar till Azure Container Instances.

Om du vill kontrol lera token `token_auth_enabled` -autentisering använder du parametern när du skapar eller uppdaterar en distribution.

Om token-autentisering har Aktiver ATS kan `get_token` du använda metoden för att hämta en Bearer-token och förfallo tiden för token:

```python
token, refresh_by = service.get_tokens()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter det att token `refresh_by` har uppnåtts. 

## <a name="request-data"></a>Data för programbegäranden

REST API: et förväntar sig att brödtexten i begäran är ett JSON-dokument med följande struktur:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Strukturen för data måste matcha vilka bedömnings skript och modell i tjänsten expect. Bedömningsskriptet kan ändra data innan det skickas till modellen.

Till exempel modellen i den [träna i anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exempel förväntar sig en matris med 10 tal. Bedömnings skriptet för det här exemplet skapar en numpy-matris från begäran och skickar den till modellen. I följande exempel visas de data som den här tjänsten förväntas:

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

Webbtjänsten kan acceptera flera uppsättningar av data i en begäran. Den returnerar ett JSON-dokument som innehåller en matris av svar.

### <a name="binary-data"></a>Binära data

Information om hur du aktiverar stöd för binära data i tjänsten finns i [binära data](how-to-deploy-and-where.md#binary).

### <a name="cross-origin-resource-sharing-cors"></a>Resurs delning mellan ursprung (CORS)

Information om hur du aktiverar CORS-stöd i tjänsten finns i [resurs delning mellan ursprung](how-to-deploy-and-where.md#cors).

## <a name="call-the-service-c"></a>Anropa tjänsten (C#)

Det här exemplet visar hur du använder C# att anropa webbtjänsten som skapats från den [träna i anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exempel:

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
            // Set the scoring URI and authentication key
            string scoringUri = "<your web service URI>";
            string authKey = "<your key>";

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

Resultatet som returneras liknar följande JSON-dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Anropa tjänsten (Go)

Det här exemplet visar hur du använder Go för att anropa webbtjänst som skapats från den [träna i anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exempel:

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
// Set to the authentication key (if any) for your service
var authKey string = "<your key>"

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

Resultatet som returneras liknar följande JSON-dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Anropa tjänsten (Java)

Det här exemplet visar hur du använder Java för att anropa webbtjänst som skapats från den [träna i anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exempel:

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
        // If using authentication, replace with the auth key
        String key = "<your key>";
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

Resultatet som returneras liknar följande JSON-dokument:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Anropa tjänsten (Python)

Det här exemplet visar hur du använder Python för att anropa webbtjänst som skapats från den [träna i anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exempel:

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key
key = '<your key>'

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

Resultatet som returneras liknar följande JSON-dokument:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Använda tjänsten från Power BI

Power BI stöder användning av Azure Machine Learning webb tjänster för att utöka data i Power BI med förutsägelser. 

Om du vill generera en webb tjänst som stöds för användning i Power BI måste schemat ha stöd för det format som krävs av Power BI. [Lär dig hur du skapar ett schema som stöds av Power BI](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#example-script-with-dictionary-input-support-consumption-from-power-bi).

När webb tjänsten har distribuerats kan den förbrukas från Power BI data flöden. [Lär dig hur du använder en Azure Machine Learning-webb tjänst från Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Nästa steg

Om du vill visa en referens arkitektur för real tids poängsättning av python-och djup inlärnings modeller går du till [Azure Architecture Center](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
