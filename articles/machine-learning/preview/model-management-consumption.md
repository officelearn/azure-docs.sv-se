---
title: "Azure Machine Learning modellen Management web service-förbrukningen | Microsoft Docs"
description: "Det här dokumentet beskriver stegen och begrepp som är involverad i förbrukar distribueras med modellhantering av i Azure Machine Learning-webbtjänster."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: b8641cd2d4a34821b7cf0e644345f0904bad294a
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="consuming-web-services"></a>Konsumera webbtjänster
När du distribuerar en modell som en webbtjänst i realtid kan du skicka data och få förutsägelser från en mängd olika plattformar och program. Webbtjänsten realtid visar ett REST-API för att hämta förutsägelser. Du kan skicka data till webbtjänsten i formatet en eller flera rader för att hämta en eller flera förutsägelser i taget.

Med den [Azure Machine Learning-webbtjänst](model-management-service-deploy.md), ett externt program synkront kommunicerar med en förutsägelsemodell genom att göra HTTP POST-anrop till tjänsten URL-Adressen. Om du vill göra ett webbtjänstanrop måste klientprogrammet ange API-nyckeln som skapas när du distribuerar en förutsägelse och placera data för begäran i begärandetexten POST.

Observera att API-nycklar är bara tillgängliga i distributionsläget klustret. Lokala webbtjänster saknar nycklar.

## <a name="service-deployment-options"></a>Distributionsalternativ för tjänsten
Azure Machine Learning-webbtjänster kan distribueras till de molnbaserade kluster för produktions- och testa scenarier och lokala arbetsstationer med docker-motorn. Funktionen för förutsägelsemodell i båda fallen förblir densamma. Kluster-baserad distribution ger skalbara och performant lösning baserad på Azure Container Service medan den lokala distributionen kan användas för felsökning. 

Azure Machine Learning CLI och API ger praktiska kommandon för att skapa och hantera compute miljöer för distributioner med hjälp av den ```az ml env``` alternativet. 

## <a name="list-deployed-services-and-images"></a>Lista över distribuerade tjänster och bilder
Du kan ange den för tillfället distribuerade tjänster och Docker bilder med hjälp av CLI kommandot ```az ml service list realtime -o table```. Observera att det här kommandot fungerar alltid i samband med den aktuella beräknings-miljön. Tjänster som har distribuerats visas inte i en miljö som inte är inställd på aktuellt. Att ställa in miljön används ```az ml env set```. 

## <a name="get-service-information"></a>Hämta tjänstinformation
När webbtjänsten har distribuerats, använder du följande kommando få tjänstens Webbadress och annan information för att anropa tjänstslutpunkten. 

```
az ml service usage realtime -i <service name>
```

Det här kommandot skriver ut tjänst-URL, obligatoriska begärandehuvuden, swagger URL och exempeldata för att anropa tjänsten om API-schemat för tjänsten angavs vid tidpunkten för distribution.

Du kan testa tjänsten direkt från CLI utan att skapa en HTTP-begäran genom att ange CLI Exempelkommando med indata:

```
az ml service run realtime -i <service name> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Hämta service API-nyckel
För att få webbtjänstens, använder du följande kommando:

```
az ml service keys realtime -i <web service id>
```
När du skapar HTTP-begäran använder nyckeln i authorization-huvud: ”tillstånd” ”: ägar <key>”

## <a name="get-the-service-swagger-description"></a>Hämta tjänstbeskrivning för Swagger
Om tjänsten API-schemat, tjänstslutpunkten skulle exponera en Swagger-dokument på ```http://<ip>/api/v1/service/<service name>/swagger.json```. Swagger-dokument kan användas för att automatiskt generera tjänstklienten och utforska förväntade indata och annan information om tjänsten.

## <a name="get-service-logs"></a>Hämta service-loggar
Om du vill förstå tjänstbeteende och diagnostisera problem, finns det flera sätt att hämta loggarna för tjänsten:
- CLI-kommandot ```az ml service logs realtime -i <service id>```. Det här kommandot fungerar i både klustret och lokala lägen.
- Om du har aktiverat tjänsten loggning på distributionen skickas också service-loggar till AppInsight. Kommandot CLI ```az ml service usage realtime -i <service id>``` visar AppInsight-URL. Observera att AppInsight loggar kan vara fördröjd med 2-5 minuter.
- Klustret loggar kan ses via Kubernetes-konsol som är ansluten när du ställer in aktuell klustermiljön med ```az ml env set```
- Lokala docker loggar är tillgängliga via docker-motorloggar när tjänsten körs lokalt.

## <a name="call-the-service-using-c"></a>Anropa tjänsten med hjälp av C#
Använda service-URL: en för att skicka en begäran från en C#-Konsolapp. 

1. Skapa en ny Konsolapp i Visual Studio: 
    * Klicka på på menyn, Arkiv -> Ny -> projekt
    * Under Visual Studio C#, klicka på skrivbordet för klassen och välj sedan konsolprogram.
2. Ange _MyFirstService_ som namnet på projektet och klicka sedan på OK.
3. Ange referenser i projektet refererar till _System.Net_, och _System.Net.Http_.
4. Klicka på Verktyg -> Pakethanteraren NuGet Package Manager-konsolen -> och sedan installera Microsoft.AspNet.WebApi.Client-paketet.
5. Öppna filen Program.cs och Ersätt Koden med följande kod:
6. Uppdatering av _SERVICE_URL_ och _API_KEY_ parametrar med information från webbtjänsten.
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

## <a name="call-the-web-service-using-python"></a>Anropa webbtjänsten använder Python
Använda Python för att skicka en begäran till realtid webbtjänsten. 

1. Kopiera följande kodexempel till en ny Python-fil.
2. Uppdatera data och URL: en api_key parametrar. Ta bort ”tillstånd”-huvudet för lokala webbtjänster.
3. Kör kod. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, data, headers=headers)
resp.text
```
