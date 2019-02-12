---
title: Använda webbtjänst
titleSuffix: Azure Machine Learning Studio
description: När en machine learning-tjänsten har distribuerats från Azure Machine Learning Studio, kan RESTFul-webb-tjänsten användas som i realtid begäranden och svar-tjänsten eller som en tjänst för batch-körningen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 4af7af6616b5415cd13abacd6aca13bd412d4ad8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998123"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-web-service"></a>Hur du använder en Azure Machine Learning Studio-webbtjänst

När du distribuerar en Azure Machine Learning förutsägelsemodell som en webbtjänst, kan du kan använda ett REST-API för att skicka data och få förutsägelser. Du kan skicka data i realtid eller i batchläge.

Du hittar mer information om hur du skapar och distribuerar en Machine Learning-webbtjänst med hjälp av Machine Learning Studio här:

* En självstudiekurs om hur du skapar ett experiment i Machine Learning Studio finns i [skapa ditt första experiment](create-experiment.md).
* Mer information om hur du distribuerar en webbtjänst finns i [distribuera en Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).
* Mer information om Machine Learning finns på den [Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Översikt
Med Azure Machine Learning webbtjänsten kommunicerar ett externt program med en arbetsflödesbaserad poängmodell i Machine Learning i realtid. Ett Machine Learning webbtjänstanrop returnerar förutsägelser till ett externt program. Om du vill göra ett Machine Learning-webbtjänstanrop skickar du en API-nyckel som skapas när du distribuerar en förutsägelse. Machine Learning-webbtjänst baseras på REST, ett populärt arkitekturval för programmeringsprojekt.

Azure Machine Learning har två typer av tjänster:

* Request-Response Service (RR) – låga svarstider och mycket skalbar tjänst som tillhandahåller ett gränssnitt för de tillståndslösa modeller som skapas och distribueras från Machine Learning Studio.
* Batch Execution Service (BES) – en asynkron tjänst som poängsätter en batch med dataposter.

Mer information om Machine Learning-webbtjänsterna finns i [distribuera en Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Hämta en nyckel för auktorisering av Azure Machine Learning
När du distribuerar ditt experiment, genereras API-nycklar för webbtjänsten. Du kan hämta nycklarna från flera platser.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Från Microsoft Azure Machine Learning Web Services-portalen
Logga in på den [Microsoft Azure Machine Learning Web Services](https://services.azureml.net) portal.

Att hämta API-nyckel för det nya Machine Learning:

1. I Azure Machine Learning Web Services-portalen klickar du på **webbtjänster** den översta menyn.
2. Klicka på den webbtjänst som du vill hämta nyckeln.
3. Klicka på den översta menyn **förbruka**.
4. Kopiera och spara den **primärnyckel**.

Att hämta API-nyckel för det klassiska Machine Learning:

1. I Azure Machine Learning Web Services-portalen klickar du på **klassiska webbtjänster** den översta menyn.
2. Klicka på den webbtjänst som du arbetar.
3. Klicka på den slutpunkt som du vill hämta nyckeln.
4. Klicka på den översta menyn **förbruka**.
5. Kopiera och spara den **primärnyckel**.

### <a name="classic-web-service"></a>Klassisk webbtjänst
 Du kan också hämta en nyckel för en klassisk webbtjänst från Machine Learning Studio.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. I Machine Learning Studio, klickar du på **WEBBTJÄNSTER** till vänster.
2. Klicka på en webbtjänst. Den **API-nyckel** finns på den **INSTRUMENTPANELEN** fliken.

## <a id="connect"></a>Ansluta till en Machine Learning-webbtjänst
Du kan ansluta till en Machine Learning-webbtjänst med alla programmeringsspråk som har stöd för HTTP-begäran och svaret. Du kan visa exemplen i C#, Python och R från Machine Learning Web service hjälpsida.

**Machine Learning API-hjälp** Machine Learning API-hjälp skapas när du distribuerar en webbtjänst. Se [självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md).
Machine Learning API-hjälpen innehåller information om en förutsägelse webbtjänsten.

1. Klicka på den webbtjänst som du arbetar.
2. Klicka på den slutpunkt som du vill visa API-hjälpsidan.
3. Klicka på den översta menyn **förbruka**.
4. Klicka på **API-hjälpsidan** under den begäranden och svar eller Batch Execution slutpunkter.

**Att visa Machine Learning API-hjälp för en ny webbtjänst**

I den [Azure Machine Learning Web Services Portal](https://services.azureml.net/):

1. Klicka på **WEBBTJÄNSTER** på den översta menyn.
2. Klicka på den webbtjänst som du vill hämta nyckeln.

Klicka på **Använd webbtjänst** att hämta URI: er för begäranden och svar och tjänster för Batch-körning och exempel koden i C#, R och Python.

Klicka på **Swagger API** baserat dokumentation för att hämta Swagger för API: er som anropas från den angivna URI: er.

### <a name="c-sample"></a>C#-exempel
Om du vill ansluta till en Machine Learning-webbtjänst, en **HttpClient** skicka ScoreData. ScoreData innehåller en FeatureVector, en n-dimensionell vektor numeriska funktioner som representerar ScoreData. Du autentiserar till Machine Learning-tjänsten med en API-nyckel.

Att ansluta till en Machine Learning-webbtjänster, den **system.NET.http.Formatting** NuGet-paketet måste vara installerad.

**Installera Microsoft.AspNet.WebApi.Client NuGet i Visual Studio**

1. Publicera Download datauppsättningen från UCI: Vuxen 2 klass datauppsättning webbtjänsten.
2. Klicka på **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.
3. Välj **Install-Package system.NET.http.Formatting**.

**Att köra kodexemplet**

1. Publicera ”exempel 1: Hämta datauppsättningen från UCI: Vuxen 2 klass datauppsättning ”experiment, en del av de Machine Learning-exemplet.
2. Tilldela apiKey med nyckel från en webbtjänst. Se **hämta en nyckel för auktorisering av Azure Machine Learning** ovan.
3. Tilldela serviceUri med begäran-URI.

**Här är en fullständig begäran ut.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.Net application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Python-exempel
Om du vill ansluta till en Machine Learning-webbtjänst, den **urllib2** bibliotek för Python 2.X och **urllib.request** bibliotek för Python 3.X. Du kommer att skicka ScoreData som innehåller en FeatureVector, en n-dimensionell vektor numeriska funktioner som representerar ScoreData. Du autentiserar till Machine Learning-tjänsten med en API-nyckel.

**Att köra kodexemplet**

1. Distribuera ”exempel 1: Hämta datauppsättningen från UCI: Vuxen 2 klass datauppsättning ”experiment, en del av de Machine Learning-exemplet.
2. Tilldela apiKey med nyckel från en webbtjänst. Se den **hämta en nyckel för auktorisering av Azure Machine Learning** avsnittet i början av den här artikeln.
3. Tilldela serviceUri med begäran-URI.

**Här är en fullständig begäran ut.**
```python
import urllib2 # urllib.request for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(uri, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R-exempel

Anslut till en Machine Learning-webbtjänsten genom att använda den **RCurl** och **rjson** bibliotek att utföra begäran och bearbeta det returnerade JSON-svaret. Du kommer att skicka ScoreData som innehåller en FeatureVector, en n-dimensionell vektor numeriska funktioner som representerar ScoreData. Du autentiserar till Machine Learning-tjänsten med en API-nyckel.

**Här är en fullständig begäran ut.**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>JavaScript-exempel

Anslut till en Machine Learning-webbtjänsten genom att använda den **begäran** npm-paketet i projektet. Du använder också den `JSON` objekt för att formatera dina indata och tolka resultatet. Installera med hjälp av `npm install request --save`, eller Lägg till `"request": "*"` till package.json under `dependencies` och kör `npm install`.

**Här är en fullständig begäran ut.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```
