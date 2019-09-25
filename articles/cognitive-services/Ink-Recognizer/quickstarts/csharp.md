---
title: 'Snabbstart: Identifiera digitalt bläck med hand SKRIFTS tolken REST API ochC#'
titleSuffix: Azure Cognitive Services
description: Använd pennan tecknings tolkens API för att börja identifiera digitala penndrag.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 86e69d75c067159a4daa637984a392a393dc46fa
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211780"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Snabbstart: Identifiera digitalt bläck med hand SKRIFTS tolken REST API ochC#

Använd den här snabb starten för att börja skicka digitala penndrag till färg igenkännings-API: et. Det C# här programmet skickar en API-begäran som innehåller JSON-formaterade penndrag och hämtar svaret.

Även om det här programmet är skrivet i C#, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Normalt anropar du API: et från en digital intecknings app. I den här snabb starten skickas Penn strecks data för följande handskrivna exempel från en JSON-fil.

![en bild av handskriven text](../media/handwriting-sample.jpg)

Du hittar käll koden för den här snabb starten på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Förutsättningar

- Valfri version av [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Så här installerar du Newtonsoft. JSON som ett NuGet-paket i Visual Studio:
        1. Högerklicka på **lösnings hanteraren**
        2. Klicka på **Hantera NuGet-paket...**
        3. Sök efter `Newtonsoft.Json` och installera paketet
- Om du använder Linux/MacOS kan du köra det här programmet med [mono](https://www.mono-project.com/).

- Du hittar exempel Penn strecks data för den här snabb starten på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny konsol lösning i Visual Studio och Lägg till följande paket. 

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

2. Skapa variabler för din prenumerations nyckel och din slut punkt. Ersätt slut punkten nedan med den som har genererats för din tryck färgs igenkännings resurs. Lägg till den i hand SKRIFTS tolkens URI för att ansluta till API: et.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://<your-custom-subdomain>.cognitiveservices.azure.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Skapa en funktion för att skicka begär Anden

1. Skapa en ny async-funktion `Request` som använder variablerna som skapats ovan.

2. Ange klientens säkerhets protokoll och huvud information med hjälp av `HttpClient` ett objekt. Se till att lägga till din prenumerations nyckel `Ocp-Apim-Subscription-Key` i rubriken. Skapa sedan ett `StringContent` objekt för begäran.
 
3. Skicka begäran med `PutAsync()`. Returnera svaret om begäran lyckas.  
    
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

## <a name="send-an-ink-recognition-request"></a>Skicka en begäran om hand SKRIFTS igenkänning

1. Skapa en ny funktion som `recognizeInk()`kallas. Skapa begäran och skicka den genom att anropa `Request()` funktionen med din slut punkt, prenumerations nyckel, URL: en för API: et och den digitala Penn linjens data.

2. Deserialisera JSON-objektet och skriv det till-konsolen. 
    
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

## <a name="load-your-digital-ink-data"></a>Läs in digitala pennan tecknings data

Skapa en funktion som `LoadJson()` kallas för att läsa in JSON-filen med bläck data. Använd en `StreamReader` och `JsonTextReader` för att skapa `JObject` en och returnera den.
    
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

1. I appens huvud metod läser du in dina JSON-data med funktionen som du skapade ovan. 

2. Anropa funktionen `recognizeInk()` som skapats ovan. Använd `System.Console.ReadKey()` för att låta konsol fönstret vara öppet när du har kört programmet.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Kör programmet och Visa svaret

Kör appen. Ett lyckat svar returneras i JSON-format. Du kan också hitta JSON-svaret på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://go.microsoft.com/fwlink/?linkid=2089907)


Ta en titt på följande exempel program på GitHub för att se hur API: et för färg igenkänning fungerar i en digital inkungs app:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)
