---
title: Azure Machine Learning-modellhantering web service-förbrukning | Microsoft Docs
description: Det här dokumentet beskriver de steg och koncept som ingår i förbrukar distribueras med modellhantering i Azure Machine Learning-webbtjänster.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ROBOTS: NOINDEX
ms.openlocfilehash: c01198a78a32c460bd147e1e160358271b80eef5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950643"
---
# <a name="consuming-web-services"></a>Konsumera webbtjänster

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


När du distribuerar en modell som en webbtjänst i realtid kan du skicka data och få förutsägelser från en mängd olika plattformar och program. Webbtjänsten i realtid visar ett REST-API för att få förutsägelser. Du kan skicka data till webbtjänsten i formatet för en eller flera rader att hämta en eller flera förutsägelser i taget.

Med den [Azure Machine Learning-webbtjänster](model-management-service-deploy.md), kommunicerar ett externt program synkront med en förebyggande modell genom att göra HTTP POST-anrop till tjänstens URL. Om du vill göra ett webbtjänstanrop måste klientprogrammet att ange API-nyckel som skapas när du distribuerar en förutsägelse och placerar begärandedata i begärandetexten INLÄGG.

> [!NOTE]
> Observera att API-nycklar är bara tillgängliga i distributionsläget för klustret. Lokala webbtjänster har inte nycklar.

## <a name="service-deployment-options"></a>Distributionsalternativ för tjänsten
Azure Machine Learning-webbtjänster kan distribueras till klustren molnbaserade för både produktions- och testscenarier och på lokala arbetsstationer med hjälp av docker-motorn. Funktionen för förutsägande modell i båda fallen förblir densamma. Kluster-baserade distributionen tillhandahåller skalbara och högpresterande lösningen som baseras på Azure Container Services lokal distribution kan användas för felsökning. 

Azure Machine Learning CLI och API innehåller användbara kommandon för att skapa och hantera olika miljöer för distribution av tjänster med hjälp av den ```az ml env``` alternativet. 

## <a name="list-deployed-services-and-images"></a>Visa lista över distribuerade tjänster och bilder
Du kan lista de för tillfället distribuerade tjänster och Docker-avbildningar med CLI-kommando ```az ml service list realtime -o table```. Observera att det här kommandot fungerar alltid i samband med den aktuella compute-miljön. Tjänster som har distribuerats visas inte i en miljö som inte är inställt på att aktuellt. Ange hur miljön ```az ml env set```. 

## <a name="get-service-information"></a>Hämta tjänstinformation
När webbtjänsten har distribuerats, kan du använda kommandona nedan för att hämta tjänstens URL och annan information för att anropa tjänsteslutpunkt. 

```
az ml service usage realtime -i <web service id>
```

Det här kommandot skriver ut tjänstens URL, krävs begärandehuvuden, swagger URL och exempeldata för att anropa tjänsten om tjänstens API schema angavs vid tidpunkten för distribution.

Du kan testa tjänsten direkt från CLI utan att skapa en HTTP-begäran, genom att ange exemplet CLI-kommando med indata:

```
az ml service run realtime -i <web service id> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Hämta service API-nyckel
Om du vill hämta nyckel för web, använder du följande kommando:

```
az ml service keys realtime -i <web service id>
```
När du skapar HTTP-begäran kan du använda nyckeln i auktoriseringsrubriken: ”Authorization” ”: ägar <key>”

## <a name="get-the-service-swagger-description"></a>Hämta tjänstbeskrivning för Swagger
Om service API-schemat, tjänstslutpunkten skulle exponera en Swagger-dokument på ```http://<ip>/api/v1/service/<service name>/swagger.json```. Swagger-dokument kan användas för att automatiskt generera tjänstklienten och utforska förväntade indata och annan information om tjänsten.

## <a name="get-service-logs"></a>Hämta tjänstloggar
För att förstå tjänstbeteendet och diagnostisera problem, finns det flera sätt att hämta loggarna för tjänsten:
- CLI-kommando ```az ml service logs realtime -i <service id>```. Det här kommandot fungerar i både kluster- och lokala lägen.
- Om tjänsten loggning är aktiverat vid distributionen skickas också service-loggarna till AppInsight. CLI-kommando ```az ml service usage realtime -i <service id>``` visar AppInsight-URL: en. Observera att AppInsight-loggarna kan fördröjas med 2 – 5 minuter.
- Klustret loggar kan ses via Kubernetes-konsol som är ansluten när du ställer in den aktuella miljön i klustret med ```az ml env set```
- Lokala docker-loggar är tillgängliga via docker-motorloggar när tjänsten körs lokalt.

## <a name="call-the-service-using-c"></a>Anropa tjänsten med C#
Använd tjänstens URL för att skicka en begäran från en C#-Konsolapp. 

1. Skapa en ny Konsolapp i Visual Studio: 
    * I menyn klickar du på, Arkiv -> Nytt projekt ->
    * Under Visual Studio C#, klickar du på Windows-klass Desktop och välj sedan Konsolapp.
2. Ange `MyFirstService` som namnet på projektet och klicka sedan på OK.
3. Ange referenser i projektreferenserna, `System.Net`, och `System.Net.Http`.
4. Klicka på Verktyg -> NuGet Package Manager -> Package Manager-konsolen och sedan installera den **system.NET.http.Formatting** paketet.
5. Öppna **Program.cs** , och Ersätt Koden med följande kod:
6. Uppdatera den `SERVICE_URL` och `API_KEY` parametrar med information från din webbtjänst.
7. Kör projektet.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            //For local web service, comment out this line.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>Anropa webbtjänsten med hjälp av Python
Använd Python för att skicka en begäran till din webbtjänst i realtid. 

1. Kopiera följande kodexempel till en ny Python-fil.
2. Uppdatera data, URL: en och api_key parametrarna. Ta bort rubriken ”tillstånd” för lokala webbtjänster.
3. Kör koden. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, body, headers=headers)
resp.text
```
