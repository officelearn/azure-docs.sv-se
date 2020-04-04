---
title: Använda webbtjänst
titleSuffix: ML Studio (classic) - Azure
description: När en maskininlärningstjänst har distribuerats från Azure Machine Learning Studio (klassisk) kan TJÄNSTEN RESTFul Web förbrukas antingen som tjänst för begäran-svar i realtid eller som en batchkörningstjänst.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: b97fe6e55e2c36b6f101071e702952f529146281
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631664"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Så här använder du en azure Machine Learning Studio -webbtjänst (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

När du distribuerar en Azure Machine Learning Studio (klassisk) prediktiv modell som en webbtjänst kan du använda ett REST API för att skicka data och få förutsägelser. Du kan skicka data i realtid eller i batchläge.

Du hittar mer information om hur du skapar och distribuerar en Machine Learning Web-tjänst med Machine Learning Studio (klassiskt) här:

* En självstudiekurs om hur du skapar ett experiment i Machine Learning Studio (klassiskt) finns i [Skapa ditt första experiment](create-experiment.md).
* Mer information om hur du distribuerar en webbtjänst finns i [Distribuera en machine learning-webbtjänst](deploy-a-machine-learning-web-service.md).
* Mer information om Maskininlärning i allmänhet finns i [Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Översikt
Med webbtjänsten Azure Machine Learning kommunicerar ett externt program med en machine learning-arbetsflödesbedömningsmodell i realtid. Ett Machine Learning Web service-anrop returnerar förutsägelseresultat till ett externt program. Om du vill ringa ett Machine Learning Web-tjänstanrop skickar du en API-nyckel som skapas när du distribuerar en förutsägelse. Machine Learning Web-tjänsten är baserad på REST, ett populärt arkitekturval för webbprogrammeringsprojekt.

Azure Machine Learning Studio (klassisk) har två typer av tjänster:

* RRS (Request-Response Service) – En låg latens, mycket skalbar tjänst som tillhandahåller ett gränssnitt till de tillståndslösa modeller som skapats och distribuerats från Machine Learning Studio (klassisk).
* BATCH Execution Service (BES) – En asynkron tjänst som gör en batch för dataposter.

Mer information om Machine Learning Web-tjänster finns i [Distribuera en machine learning-webbtjänst](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Hämta en auktoriseringsnyckel
När du distribuerar experimentet genereras API-nycklar för webbtjänsten. Du kan hämta nycklarna från flera platser.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Från Microsoft Azure Machine Learning Web Services-portalen
Logga in på [Microsoft Azure Machine Learning Web Services-portalen.](https://services.azureml.net)

Så här hämtar du API-nyckeln för en ny machine learning-webbtjänst:

1. Klicka på **Webbtjänster** på portalen för Azure Machine Learning Web Services på den översta menyn.
2. Klicka på den webbtjänst som du vill hämta nyckeln för.
3. Klicka på **Förbruka**på den övre menyn .
4. Kopiera och spara **primärnyckeln**.

Så här hämtar du API-nyckeln för en webbkameratjänst för klassisk maskininlärning:

1. Klicka på **Klassiska webbtjänster** på portalen för Maskininlärning av webbtjänster på den översta menyn.
2. Klicka på den webbtjänst som du arbetar med.
3. Klicka på den slutpunkt som du vill hämta nyckeln för.
4. Klicka på **Förbruka**på den övre menyn .
5. Kopiera och spara **primärnyckeln**.

### <a name="classic-web-service"></a>Klassisk webbtjänst
 Du kan också hämta en nyckel för en klassisk webbtjänst från Machine Learning Studio (klassisk).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (klassisk)
1. I Machine Learning Studio (klassisk) klickar du på **WEBBTJÄNSTER** till vänster.
2. Klicka på en webbtjänst. **API-nyckeln** finns på fliken **INSTRUMENTPANEL.**

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Ansluta till en machine learning-webbtjänst
Du kan ansluta till en Machine Learning Web-tjänst med valfritt programmeringsspråk som stöder HTTP-begäran och svar. Du kan visa exempel i C#, Python och R från en hjälpsida för machine learning-webbtjänst.

**Hjälp om MASKININlärnings-API** Maskininlärnings-API-hjälp skapas när du distribuerar en webbtjänst. Se [handledning 3: distribuera kreditriskmodell](tutorial-part3-credit-risk-deploy.md).
Hjälpen för Machine Learning API innehåller information om en förutsägelsewebbtjänst.

1. Klicka på den webbtjänst som du arbetar med.
2. Klicka på den slutpunkt som du vill visa api-hjälpsidan för.
3. Klicka på **Förbruka**på den övre menyn .
4. Klicka på **API-hjälpsidan** under slutpunkterna Begäran-svar eller Batch-körning.

**Så här visar du hjälp med Maskininlärnings-API för en ny webbtjänst**

I [Portalen för Azure Machine Learning Web Services](https://services.azureml.net/):

1. Klicka på **WEBBTJÄNSTER** på den övre menyn.
2. Klicka på den webbtjänst som du vill hämta nyckeln för.

Klicka på **Använd webbtjänst** om du vill hämta URI:erna för tjänsten Förfråm och batchkörning och exempelkoden i C#, R och Python.

Klicka på **Swagger API** för att få Swagger-baserad dokumentation för API:er som anropas från de medföljande URI:erna.

### <a name="c-sample"></a>C# Exempel
Om du vill ansluta till en Machine Learning Web-tjänst använder du en HttpClient-passeringspoängdata. **HttpClient** ScoreData innehåller en FeatureVector, en n-dimensionell vektor med numeriska funktioner som representerar ScoreData. Du autentiserar till machine learning-tjänsten med en API-nyckel.

Om du vill ansluta till en Machine Learning Web-tjänst måste **Paketet Microsoft.AspNet.WebApi.Client** NuGet vara installerat.

**Installera Microsoft.AspNet.WebApi.Client NuGet i Visual Studio**

1. Publicera datauppsättningen Hämta från UCI: Adult 2-klassens datauppsättningswebbtjänst.
2. Klicka på **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.
3. Välj **Installera-paket Microsoft.AspNet.webapi.client**.

**Så här kör du kodexemplet**

1. Publicera experimentet "Exempel 1: Ladda ned datauppsättning från UCI: Adult 2 class dataset", en del av exempelsamlingen machine learning.
2. Tilldela apiKey med nyckeln från en webbtjänst. Se **Hämta en auktoriseringsnyckel** ovan.
3. Tilldela serviceUri med URI för begäran.

**Här är vad en fullständig begäran kommer att se ut.**
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
Om du vill ansluta till en Machine Learning Web-tjänst använder du **urllib2-biblioteket** för Python 2.X och **urllib.request-biblioteket** för Python 3.X. Du kommer att passera ScoreData, som innehåller en FeatureVector, en n-dimensionell vektor av numeriska funktioner som representerar ScoreData. Du autentiserar till machine learning-tjänsten med en API-nyckel.

**Så här kör du kodexemplet**

1. Distribuera experimentet "Exempel 1: Hämta datauppsättning från UCI: Adult 2 class dataset", en del av exempelsamlingen maskininlärning.
2. Tilldela apiKey med nyckeln från en webbtjänst. Se avsnittet **Hämta nyckelavsnittet för auktorisering** i början av den här artikeln.
3. Tilldela serviceUri med URI för begäran.

**Här är vad en fullständig begäran kommer att se ut.**
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

Om du vill ansluta till en machine learning-webbtjänst använder du **biblioteken RCurl** och **rjson** för att göra begäran och bearbeta det returnerade JSON-svaret. Du kommer att passera ScoreData, som innehåller en FeatureVector, en n-dimensionell vektor av numeriska funktioner som representerar ScoreData. Du autentiserar till machine learning-tjänsten med en API-nyckel.

**Här är vad en fullständig begäran kommer att se ut.**
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

Om du vill ansluta till en machine learning-webbtjänst använder du **paketet för begäran** npm i projektet. Du kommer också `JSON` att använda objektet för att formatera indata och tolka resultatet. Installera `npm install request --save`med , `"request": "*"` eller lägg till `dependencies` i `npm install`package.json under och kör .

**Här är vad en fullständig begäran kommer att se ut.**
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
