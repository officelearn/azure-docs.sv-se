---
title: 'Snabbstart: Känner igen digitalt bläck med INK Recognizer REST API och Node.js'
titleSuffix: Azure Cognitive Services
description: Använd API:et för bläckreformat för att börja känna igen digitala pennstreck i den här snabbstarten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: a37f2b7044fcba04ca18093aa73563961e9e35de
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448133"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Snabbstart: Känner igen digitalt bläck med INK Recognizer REST API och JavaScript

Använd den här snabbstarten för att börja använda INK Recognizer-API:et på digitala pennstreck. Det här JavaScript-programmet skickar en API-begäran som innehåller JSON-formaterade pennstrecksdata och visar svaret.

Medan denna ansökan är skriven i Javascript och körs i din webbläsare, är API en RESTful webbtjänst kompatibel med de flesta programmeringsspråk.

Vanligtvis anropar du API:et från en digital inking-app. Den här snabbstarten skickar pennstrecksdata för följande handskrivna exempel från en JSON-fil.

![en bild av handskriven text](../media/handwriting-sample.jpg)

Källkoden för den här snabbstarten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Krav

- En webbläsare
- Exempelvis pennstreckdata för den här snabbstarten finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Skapa en resurs för pennanteckningskone igen

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. Skapa en ny `.html` fil i din favorit-IDE eller redigerare. Lägg sedan till grundläggande HTML till den för koden vi lägger till senare.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. Lägg `<body>` till följande html i taggen:
    1. Två textområden för att visa JSON-begäran och svar.
    2. En knapp för `recognizeInk()` att anropa funktionen som kommer att skapas senare.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Läsa in exempel på JSON-data

1. Skapa `<script>` en variabel för sampleJson i taggen. Skapa sedan en JavaScript-funktion med namnet `openFile()` som öppnar en utforskare så att du kan välja din JSON-fil. När `Recognize ink` du klickar på knappen anropas den här funktionen och läser filen.
2. Använd `FileReader` ett objekts `onload()` funktion för att bearbeta filen asynkront. 
    1. Ersätt `\n` alla `\r` tecken i filen med en tom sträng. 
    2. Används `JSON.parse()` för att konvertera texten till giltig JSON
    3. Uppdatera `request` textrutan i programmet. Används `JSON.stringify()` för att formatera JSON-strängen. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Skicka en begäran till API:et för bläckre recognizeer

1. `<script>` Skapa en funktion som `recognizeInk()`heter . Den här funktionen anropar senare API:et och uppdaterar sidan med svaret. Lägg till koden från följande steg i den här funktionen. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Skapa variabler för slutpunkts-URL:en, prenumerationsnyckeln och exemplet JSON. Skapa sedan `XMLHttpRequest` ett objekt för att skicka API-begäran. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Skapa returfunktionen för `XMLHttpRequest` objektet. Den här funktionen tolkar API-svaret från en lyckad begäran och visar det i programmet. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Skapa felfunktionen för begärandeobjektet. Den här funktionen loggar felet till konsolen. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Skapa en funktion för `onreadystatechange` egenskapen för begärandeobjektet. När begäran objektets beredskap tillstånd ändras, kommer ovanstående retur och fel funktioner tillämpas.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Skicka API-begäran. Lägg till din `Ocp-Apim-Subscription-Key` prenumerationsnyckel i `content-type` sidhuvudet och ställ in`application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Kör programmet och visa svaret

Det här programmet kan köras i din webbläsare. Ett lyckat svar returneras i JSON-format. Du kan också hitta JSON-svaret på [GitHub:](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://go.microsoft.com/fwlink/?linkid=2089907)

Om du vill se hur API:et för bläckigenkänning fungerar i en digital pennanteckningsapp kan du ta en titt på följande exempelprogram på GitHub:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)
