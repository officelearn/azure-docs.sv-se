---
title: 'Snabbstart: Identifiera avvikelser i dina time series-data med hjälp av Avvikelseidentifiering detektor REST API och C# | Microsoft Docs'
description: 'Använda API: T för Avvikelseidentifiering detektor för att identifiera avvikelser i dina dataserien som en batch eller på strömmande data.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 7aa171a49ea03769c3ecbb5d35ae31ac6fae052e
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473263"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Snabbstart: Identifiera avvikelser i dina time series-data med hjälp av Avvikelseidentifiering detektor REST API ochC# 

Använd den här snabbstarten för att börja använda identifiering av avvikelser detektor API: er lägena för att identifiera avvikelser i tidsseriedata. Detta C# programmet skickar två API-begäranden som innehåller JSON-formaterade time series-data och får svar.

| API-begäran                                        | Programutdata                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Identifiera avvikelser som en batch                        | JSON-svar som innehåller avvikelseidentifiering status (och andra data) för varje datapunkt i time series-data och positioner för alla identifierade avvikelser. |
| Identifiera avvikelser status för senaste datapunkt | JSON-svar som innehåller avvikelseidentifiering status (och andra data) för den senaste datapunkten i time series-data.                                                                                                                                         |

 Även om det här programmet är skrivet i C#, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Förutsättningar

- Valfri version av [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Så här installerar du Newtonsoft.Json som NuGet-paket i Visual studio:
        1. Högerklicka på den **lösningen Manager**
        2. Klicka på **hantera NuGet-paket...**
        3. Sök efter `Newtonsoft.Json` och installera paketet
- Om du använder Linux/Mac OS, det här programmet kan kördes med [Mono](http://www.mono-project.com/).

- Pekar en JSON-fil som innehåller time series-data. Exempeldata för den här snabbstarten finns på [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny konsol-lösning i Visual Studio och Lägg till följande paket. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Skapa variabler för din prenumerationsnyckel och slutpunkten. Nedan visas URI: er som du kan använda för identifiering av avvikelser. Dessa kommer att läggas till tjänstens slutpunkt senare för att skapa API: et begär URL: er.

    |Identifieringsmetod  |URI  |
    |---------|---------|
    |Batch-identifiering    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Identifiering på den senaste tidpunkten för data     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begäranden

1. Skapa en ny async-funktion som kallas `Request` som tar de variabler som skapades ovan.

2. Ange klientens protokoll för säkerhet och rubrik informationen med hjälp av en `HttpClient` objekt. Se till att lägga till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik. Skapa sedan en `StringContent` objekt för begäran.
 
3. Skicka begäran med `PostAsync()`. Om begäran lyckas returnera svaret.  

```csharp
static async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        if (res.IsSuccessStatusCode){
            return await res.Content.ReadAsStringAsync();
        }
        else{
            return $"ErrorCode: {res.StatusCode}";
        }
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Identifiera avvikelser som en batch

1. Skapa en ny funktion som kallas `detectAnomaliesBatch()`. Skapa begäran och skicka den genom att anropa den `Request()` funktion med din slutpunkt, prenumerationsnyckel, URL-Adressen för batch-avvikelseidentifiering och time series-data.

2. Deserialisera JSON-objekt och skriva den till konsolen.

3. Hitta positioner av avvikelser i datauppsättningen. Svarets `isAnomaly` fältet innehåller en matris med booleska värden som anger om en datapunkt är en avvikelse. Konvertera det till en sträng med objektet response `ToObject<bool[]>()` funktion.

4. Gå igenom matrisen och skriva ut index för någon `true` värden. Dessa värden motsvarar index för avvikande datapunkter, om några.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
    System.Console.WriteLine("\n Anomalies detected in the following data positions:");
    for (var i = 0; i < anomalies.Length; i++) {
        if (anomalies[i]) {
            System.Console.Write(i + ", ");
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelser status för senaste datapunkt

1. Skapa en ny funktion som kallas `detectAnomaliesLatest()`. Skapa begäran och skicka den genom att anropa den `Request()` funktion med din slutpunkt, prenumerationsnyckel, URL-Adressen för senaste återställningspunkt avvikelseidentifiering och time series-data.

2. Deserialisera JSON-objekt och skriva den till konsolen. 

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\n Determining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Läsa in time series-data och skicka begäran

1. I den huvudsakliga metoden för ditt program att läsa in JSON time series-data med `File.ReadAllText()`. 

2. Anropa de funktioner för identifiering av avvikelser skapade ovan. Använd `System.Console.ReadKey()` att hålla konsolfönstret öppen när du har kört programmet.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON-format. Klicka på länkarna nedan för att visa JSON-svar på GitHub:
* [Exempelsvar batch identifiering](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Senaste återställningspunkt identifiering exempelsvar](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)