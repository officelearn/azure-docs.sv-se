---
title: Använda en Azure Machine Learning-webbtjänst | Microsoft Docs
description: När machine learning-tjänsten har distribuerats, kan RESTFul-webbtjänst som är tillgängliga användas som realtid begäran och svar tjänst eller som en tjänst för batch-körningen.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.openlocfilehash: b89fb0fbb499fa06c9e56f02937b1c586efde9b6
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833401"
---
# <a name="how-to-consume-an-azure-machine-learning-web-service"></a>Använda en Azure Machine Learning-webbtjänst

Du kan använda en REST-API för att skicka data och få förutsägelser när du distribuerar en Azure Machine Learning förutsägelsemodell som en webbtjänst. Du kan skicka data i realtid eller i batch-läge.

Du hittar mer information om hur du skapar och distribuerar en Machine Learning-webbtjänst med Machine Learning Studio här:

* En självstudiekurs om hur du skapar ett experiment i Machine Learning Studio finns [skapa ditt första experiment](create-experiment.md).
* Mer information om hur du distribuerar en webbtjänst finns [distribuera en Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).
* Mer information om Machine Learning finns på den [Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Översikt
Ett externt program kommunicerar med en Machine Learning arbetsflöde bedömningsprofil modell i realtid med Azure Machine Learning webbtjänsten. Ett Machine Learning webbtjänstanrop returnerar förutsägelse resultat till ett externt program. För att göra en Machine Learning-webbtjänst anropa kan överföra du en API-nyckel som skapas när du distribuerar en förutsägelse. Webbtjänsten för Machine Learning baseras på REST, en populär arkitekturval för webb-programmering projekt.

Azure Machine Learning har två typer av tjänster:

* Svar på begäranden tjänsten (RR) – en låg latens, mycket skalbar tjänst som tillhandahåller ett gränssnitt för tillståndslösa modeller som skapas och distribueras från Machine Learning Studio.
* Batch Execution Service (BES) – en asynkron tjänsten som resultat en batch för dataposter.

Mer information om Machine Learning-webbtjänster finns [distribuera en Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Hämta auktoriseringsnyckel Azure Machine Learning
När du distribuerar experimentet genereras API-nycklar för webbtjänsten. Du kan hämta nycklarna från flera platser.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Från portalen för Microsoft Azure Machine Learning-webbtjänster
Logga in på den [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net) portal.

Att hämta API-nyckel för en ny Machine Learning-webbtjänst:

1. I Azure Machine Learning-webbtjänster-portalen klickar du på **Web Services** den översta menyn.
2. Klicka på den webbtjänst som du vill hämta nyckeln.
3. Klicka på den översta menyn **förbruka**.
4. Kopiera och spara den **primärnyckel**.

Att hämta API-nyckeln för det klassiska Machine Learning:

1. I Azure Machine Learning-webbtjänster-portalen klickar du på **klassiska webbtjänster** den översta menyn.
2. Klicka på den webbtjänst som du arbetar.
3. Klicka på den slutpunkt som du vill hämta nyckeln.
4. Klicka på den översta menyn **förbruka**.
5. Kopiera och spara den **primärnyckel**.

### <a name="classic-web-service"></a>Klassiska webbtjänst
 Du kan också hämta en nyckel för det klassiska från Machine Learning Studio.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. I Machine Learning Studio klickar du på **WEB SERVICES** till vänster.
2. Klicka på en webbtjänst. Den **API-nyckel** finns på den **INSTRUMENTPANELEN** fliken.

## <a id="connect"></a>Ansluta till en Machine Learning-webbtjänst
Du kan ansluta till en Machine Learning-webbtjänst med hjälp av programmeringsspråk som stöder HTTP-begäran och svar. Du kan visa exempel i C#, Python och R från en Machine Learning-webbtjänstens hjälpsida.

**Datorn Learning API hjälp** hjälp med Machine Learning API skapas när du distribuerar en webbtjänst. Se [Azure Machine Learning genomgången distribuerar webbtjänsten](walkthrough-5-publish-web-service.md).
Machine Learning API-hjälpen innehåller information om en förutsägelse webbtjänsten.

1. Klicka på den webbtjänst som du arbetar.
2. Klicka på den slutpunkt som du vill visa sidan API.
3. Klicka på den översta menyn **förbruka**.
4. Klicka på **API hjälpsidan** under den begäran och svar eller Batch Execution slutpunkter.

**Visa Machine Learning API hjälpa för en ny webbtjänst**

I den [Azure Machine Learning Web Services-portalen](https://services.azureml.net/):

1. Klicka på **WEB SERVICES** på den översta menyn.
2. Klicka på den webbtjänst som du vill hämta nyckeln.

Klicka på **Använd webbtjänsten** att hämta URI för begäran Reposonse och Batch-körningen tjänster och exempelkod i C#, R och Python.

Klicka på **Swagger API** att hämta Swagger-baserad dokumentationen för API: er anropas från den angivna URI: er.

### <a name="c-sample"></a>C#-exempel
Om du vill ansluta till en Machine Learning-webbtjänst en **HttpClient** skicka ScoreData. ScoreData innehåller en FeatureVector en endimensionell n vector av numeriska funktioner som representerar ScoreData. Du autentisera till tjänsten Machine Learning med en API-nyckel.

Att ansluta till en Machine Learning webbtjänst den **Microsoft.AspNet.WebApi.Client** NuGet-paketet måste vara installerad.

**Installera Microsoft.AspNet.WebApi.Client NuGet i Visual Studio**

1. Publicera datamängden för nedladdning från UCI: vuxna 2 klass dataset-webbtjänst.
2. Klicka på **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.
3. Välj **Install-Package Microsoft.AspNet.WebApi.Client**.

**Att köra kodexemplet**

1. Publicera ”exempel 1: hämta dataset från UCI: vuxen 2 klass dataset” experiment, som ingår i Machine Learning exempel samling.
2. Tilldela apiKey med nyckel från en webbtjänst. Se **hämta auktoriseringsnyckel Azure Machine Learning** ovan.
3. Tilldela serviceUri med begärd URI.

**Här är en fullständig begäran utseende.**
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

                    // Print the headers - they include the requert ID and the timestamp,
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
Om du vill ansluta till en Machine Learning-webbtjänst på **urllib2** bibliotek för Python 2.X och **urllib.request** bibliotek för Python 3.X. Du skickar ScoreData som innehåller en FeatureVector en endimensionell n vector av numeriska funktioner som representerar ScoreData. Du autentisera till tjänsten Machine Learning med en API-nyckel.

**Att köra kodexemplet**

1. Distribuera ”exempel 1: hämta dataset från UCI: vuxen 2 klass dataset” experiment, som ingår i Machine Learning exempel samling.
2. Tilldela apiKey med nyckel från en webbtjänst. Finns det **hämta auktoriseringsnyckel Azure Machine Learning** avsnitt i början av den här artikeln.
3. Tilldela serviceUri med begärd URI.

**Här är en fullständig begäran utseende.**
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

    # Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R-exempel

Om du vill ansluta till en Machine Learning-webbtjänst på **RCurl** och **rjson** bibliotek att begära och bearbeta returnerade JSON-svar. Du skickar ScoreData som innehåller en FeatureVector en endimensionell n vector av numeriska funktioner som representerar ScoreData. Du autentisera till tjänsten Machine Learning med en API-nyckel.

**Här är en fullständig begäran utseende.**
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

# Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>JavaScript-exempel

Om du vill ansluta till en Machine Learning-webbtjänst på **begäran** npm paket i projektet. Du kan också använda den `JSON` objekt för att formatera dina indata och tolka resultatet. Installera med hjälp av `npm install request --save`, eller Lägg till `"request": "*"` till package.json under `dependencies` och kör `npm install`.

**Här är en fullständig begäran utseende.**
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