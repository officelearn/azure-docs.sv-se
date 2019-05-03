---
title: 'Snabbstart: Identifiera digitala ink med Ink Igenkännande REST API ochC#'
description: Använd Ink Igenkännande API för att starta eftersom digitala ink linjer.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: f03593292289cbc093832667505da2738c2b1633
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026292"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Snabbstart: Identifiera digitala ink med Ink Igenkännande REST API ochC#

Använd den här snabbstarten för att börja skicka digitala ink linjer Ink Igenkännande-API: et. Detta C# programmet skickar en API-begäran som innehåller JSON-formaterade ink linje data och hämtar svaret.

Även om det här programmet är skrivet i C#, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Vanligtvis skulle du anropa API från en digital digital penna app. Den här snabbstarten skickar ink linje data för följande handskriven exempel från en JSON-fil.

![en bild av handskriven text](../media/handwriting-sample.jpg)

Källkoden för den här snabbstarten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Nödvändiga komponenter

- Valfri version av [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Så här installerar du Newtonsoft.Json som NuGet-paket i Visual studio:
        1. Högerklicka på den **lösningen Manager**
        2. Klicka på **hantera NuGet-paket...**
        3. Sök efter `Newtonsoft.Json` och installera paketet
- Om du använder Linux/Mac OS, det här programmet kan kördes med [Mono](http://www.mono-project.com/).

- Exempel ink linje data för den här snabbstarten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


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
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Skapa variabler för din prenumerationsnyckel och slutpunkten. Nedan visas den URI som du kan använda för pennanteckningar erkännande. Det kommer att läggas till tjänstens slutpunkt senare för att skapa API: et URl för begäran.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begäranden

1. Skapa en ny async-funktion som kallas `Request` som tar de variabler som skapades ovan.

2. Ange klientens protokoll för säkerhet och rubrik informationen med hjälp av en `HttpClient` objekt. Se till att lägga till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik. Skapa sedan en `StringContent` objekt för begäran.
 
3. Skicka begäran med `PutAsync()`. Om begäran lyckas returnera svaret.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Skicka en begäran om ink taligenkänning

1. Skapa en ny funktion som kallas `recognizeInk()`. Skapa begäran och skicka den genom att anropa den `Request()` funktion med din slutpunkt, prenumerationsnyckel, URL: en för API: et och digital ink linje data.

2. Deserialisera JSON-objekt och skriva den till konsolen. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>Läsa in digitala ink-data

Skapa en funktion som kallas `LoadJson()` att läsa in ink data JSON-fil. Använd en `StreamReader` och `JsonTextReader` att skapa en `JObject` och lämna tillbaka.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Skicka API-begäran

1. Läsa in JSON-data med hjälp av funktionen som skapades ovan i ditt program main-metoden. 

2. Anropa den `recognizeInk()` funktionen som skapades ovan. Använd `System.Console.ReadKey()` att hålla konsolfönstret öppen när du har kört programmet.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Kör programmet och visa svaret

Kör appen. Ett lyckat svar returneras i JSON-format. Du kan också hitta JSON-svar på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://go.microsoft.com/fwlink/?linkid=2089907)


Om du vill se hur den Ink-API: T fungerar i en digital digital penna app, ta en titt på de följande exempelprogram på GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C#och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript web browser-appen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilappen](https://go.microsoft.com/fwlink/?linkid=2089906)
* [SWIFT- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)
