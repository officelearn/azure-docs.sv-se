---
title: Använda webb tjänst
titleSuffix: ML Studio (classic) - Azure
description: När en Machine Learning-tjänst har distribuerats från Azure Machine Learning Studio (klassisk) kan RESTFul-webbtjänsten konsumeras antingen som real tids tjänst för begäran-svar eller som en batch-körnings tjänst.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: b97fe6e55e2c36b6f101071e702952f529146281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631664"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Använda en Azure Machine Learning Studio (klassisk)-webb tjänst

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

När du har distribuerat en Azure Machine Learning Studio (klassisk) förutsägelse modell som en webb tjänst kan du använda en REST API för att skicka IT-data och hämta förutsägelser. Du kan skicka data i real tid eller i batchläge.

Du hittar mer information om hur du skapar och distribuerar en Machine Learning-webbtjänst med Machine Learning Studio (klassisk) här:

* En själv studie kurs om hur du skapar ett experiment i Machine Learning Studio (klassisk) finns i [skapa ditt första experiment](create-experiment.md).
* Mer information om hur du distribuerar en webb tjänst finns i [distribuera en Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md).
* Mer information om Machine Learning i allmänhet finns i [Machine Learning dokumentations centret](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Översikt
Med Azure Machine Learning-webbtjänsten kommunicerar ett externt program med en beräknings modell för en Machine Learning arbets flöde i real tid. Ett Machine Learning webb tjänst anrop returnerar förutsägelse resultat till ett externt program. Om du vill göra ett Machine Learning webb tjänst anrop skickar du en API-nyckel som skapas när du distribuerar en förutsägelse. Machine Learning-webbtjänsten baseras på REST, ett populärt arkitektur val för webb program projekt.

Azure Machine Learning Studio (klassisk) har två typer av tjänster:

* Request-Response service (resurs poster) – en låg fördröjning, hög skalbar tjänst som tillhandahåller ett gränssnitt för de tillstånds lösa modeller som skapas och distribueras från Machine Learning Studio (klassisk).
* BES (batch execution service) – en asynkron tjänst som poängs ätter en batch för data poster.

Mer information om Machine Learning webb tjänster finns i [distribuera en Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Hämta en nyckel för auktorisering
När du distribuerar experimentet genereras API-nycklar för webb tjänsten. Du kan hämta nycklarna från flera platser.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Från Microsoft Azure Machine Learning Web Services-portalen
Logga in på [Microsoft Azure Machine Learning Web Services-](https://services.azureml.net) portalen.

Hämta API-nyckeln för en ny Machine Learning-webb tjänst:

1. Klicka på **webb tjänster** på den översta menyn i Azure Machine Learning Web Services-portalen.
2. Klicka på den webb tjänst som du vill hämta nyckeln för.
3. Klicka på **förbruka**på den översta menyn.
4. Kopiera och spara **primär nyckeln**.

Hämta API-nyckeln för en klassisk Machine Learning-webb tjänst:

1. Klicka på **klassiska webb tjänster** på den översta menyn i Azure Machine Learning Web Services-portalen.
2. Klicka på den webb tjänst som du arbetar med.
3. Klicka på den slut punkt som du vill hämta nyckeln för.
4. Klicka på **förbruka**på den översta menyn.
5. Kopiera och spara **primär nyckeln**.

### <a name="classic-web-service"></a>Klassisk webb tjänst
 Du kan också hämta en nyckel för en klassisk webb tjänst från Machine Learning Studio (klassisk).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (klassisk)
1. I Machine Learning Studio (klassisk) klickar du på **webb tjänster** till vänster.
2. Klicka på en webb tjänst. **API-nyckeln** finns på fliken **instrument panel** .

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Ansluta till en Machine Learning-webbtjänst
Du kan ansluta till en Machine Learning-webbtjänst med valfritt programmeringsspråk som stöder HTTP-begäran och-svar. Du kan visa exempel i C#, python och R från en Machine Learning-webbtjänstens hjälp sida.

**Machine Learning API-hjälp** Machine Learning API-hjälp skapas när du distribuerar en webb tjänst. Se [självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md).
Machine Learning API-hjälpen innehåller information om en förutsägelse webb tjänst.

1. Klicka på den webb tjänst som du arbetar med.
2. Klicka på den slut punkt som du vill visa API-hjälp sidan för.
3. Klicka på **förbruka**på den översta menyn.
4. Klicka på **API-hjälp sidan** under antingen Request-Response-eller batch-körningens slut punkter.

**Visa Machine Learning API-hjälp för en ny webb tjänst**

På [Azure Machine Learning Web Services-portalen](https://services.azureml.net/):

1. Klicka på **webb tjänster** på den översta menyn.
2. Klicka på den webb tjänst som du vill hämta nyckeln för.

Klicka på **Använd webb tjänst** för att hämta URI: er för Request-Response-och batch execution-tjänsterna och exempel kod i C#, R och python.

Klicka på **Swagger API** för att hämta Swagger-baserad dokumentation för de API: er som anropas från angivna URI: er.

### <a name="c-sample"></a>C#-exempel
Om du vill ansluta till en Machine Learning-webbtjänst använder du en **httpclient** som skickar ScoreData. ScoreData innehåller en FeatureVector, en n-dimensionell Vector med numeriska funktioner som representerar ScoreData. Du autentiserar till tjänsten Machine Learning med en API-nyckel.

För att ansluta till en Machine Learning-webbtjänst måste **Microsoft. ASPNET. WebAPI. client** NuGet-paketet installeras.

**Installera Microsoft. ASPNET. WebApi. client NuGet i Visual Studio**

1. Publicera nedladdnings data uppsättningen från den här webb tjänsten för den andra klass data uppsättningen med den vuxna 2.
2. Klicka på **verktyg** > **NuGet Package Manager** > **Package Manager-konsolen**.
3. Välj **install-Package Microsoft. ASPNET. WebAPI. client**.

**Så här kör du kod exemplet**

1. Publicera "exempel 1: Ladda ned data uppsättning från" typ av data uppsättning från den andra klassen "för data uppsättning för vuxna 2", en del av Machine Learning samling
2. Tilldela apiKey med nyckeln från en webb tjänst. Se **Hämta en auktoriseringsregel** ovan.
3. Tilldela serviceUri med URI för begäran.

**Så här kommer en fullständig förfrågan att se ut.**
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
                // if you are calling this code from the UI thread of an ASP.NET application.
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
För att ansluta till en Machine Learning-webbtjänst använder du **Urllib2** -biblioteket för python 2. x och **urllib. Request** -bibliotek för python 3. x. Du kommer att skicka ScoreData, som innehåller en FeatureVector, en n-dimensionell Vector med numeriska funktioner som representerar ScoreData. Du autentiserar till tjänsten Machine Learning med en API-nyckel.

**Så här kör du kod exemplet**

1. Distribuera "exempel 1: Ladda ned data uppsättning från den här data uppsättningen för den vuxna 2 klassen", som ingår i exempel samlingen Machine Learning.
2. Tilldela apiKey med nyckeln från en webb tjänst. Se avsnittet **Hämta en auktoriseringsregel** nära början av den här artikeln.
3. Tilldela serviceUri med URI för begäran.

**Så här kommer en fullständig förfrågan att se ut.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
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

# "urllib.request.Request(url, body, headers)" for Python 3.X
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

Om du vill ansluta till en Machine Learning-webbtjänst använder du **RCurl** -och **rjson** -biblioteken för att göra begäran och bearbeta det returnerade JSON-svaret. Du kommer att skicka ScoreData, som innehåller en FeatureVector, en n-dimensionell Vector med numeriska funktioner som representerar ScoreData. Du autentiserar till tjänsten Machine Learning med en API-nyckel.

**Så här kommer en fullständig förfrågan att se ut.**
```r
library("RCurl")
library("rjson")

# Accept TLS/SSL certificates issued by public Certificate Authorities
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

Om du vill ansluta till en Machine Learning-webbtjänst använder du NPM-paketet för **begäran** i projektet. Du kan också använda `JSON` objektet för att formatera inaktuella inaktuella och analysera resultatet. Installera med `npm install request --save`eller Lägg till `"request": "*"` i Package. JSON under `dependencies` och kör. `npm install`

**Så här kommer en fullständig förfrågan att se ut.**
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
