---
title: 'Snabb start: identifiera digitalt bläck med hand SKRIFTS tolken REST API och Node.js'
titleSuffix: Azure Cognitive Services
description: Använd pennan tecknings tolkens API och Java Script för att börja identifiera digitala penndrag i den här snabb starten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ca1c18fd1ea8507c8b6a58b5f786b9686b1f3a34
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369092"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Snabb start: identifiera digitalt bläck med hand SKRIFTS tolken REST API och Java Script

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Använd den här snabb starten för att börja använda pennan tecknings tolkens API på digitala penndrag. Det här JavaScript-programmet skickar en API-begäran som innehåller JSON-formaterade penndrag och visar svaret.

Även om det här programmet är skrivet i Java Script och körs i webbläsaren, är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Normalt anropar du API: et från en digital intecknings app. I den här snabb starten skickas Penn strecks data för följande handskrivna exempel från en JSON-fil.

![en bild av handskriven text](../media/handwriting-sample.jpg)

Du hittar käll koden för den här snabb starten på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Förutsättningar

- En webbläsare
- Du hittar exempel Penn strecks data för den här snabb starten på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Skapa en tryck färgs igenkännings resurs

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. I din favorit-IDE eller-redigerare skapar du en ny `.html` fil. Lägg sedan till grundläggande HTML-kod i den för koden som vi ska lägga till senare.
    
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

2. I `<body>` taggen lägger du till följande HTML:
    1. Två text områden för att Visa JSON-begäran och-svar.
    2. En knapp för att anropa `recognizeInk()` funktionen som kommer att skapas senare.
    
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

## <a name="load-the-example-json-data"></a>Läs in exemplet med JSON-data

1. I `<script>` taggen skapar du en variabel för sampleJson. Skapa sedan en JavaScript-funktion med namnet `openFile()` som öppnar en fil Utforskare så att du kan välja din JSON-fil. När du `Recognize ink` klickar på knappen anropas den här funktionen och börjar läsa filen.
2. Använd ett `FileReader` objekts `onload()` funktion för att bearbeta filen asynkront. 
    1. Ersätt eventuella `\n` eller `\r` tecken i filen med en tom sträng. 
    2. Använd `JSON.parse()` för att konvertera texten till giltig JSON
    3. Uppdatera `request` text rutan i programmet. Används `JSON.stringify()` för att formatera JSON-strängen. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Skicka en begäran till hand SKRIFTS tolkens API

1. I `<script>` taggen skapar du en funktion som kallas `recognizeInk()` . Den här funktionen anropar sedan API: et och uppdaterar sidan med svaret. Lägg till koden från följande steg i den här funktionen. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Skapa variabler för slut punkts-URL, prenumerations nyckel och exempel-JSON. Skapa sedan ett `XMLHttpRequest` objekt för att skicka API-begäran. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Skapa funktionen Return för `XMLHttpRequest` objektet. Den här funktionen kommer att parsa API-svaret från en lyckad begäran och visa den i programmet. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Skapa funktionen Error för objektet Request. Den här funktionen loggar felet till-konsolen. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Skapa en funktion för egenskapen Request för objektet `onreadystatechange` . När det begär ande objektets beredskaps tillstånd ändras, kommer ovanstående retur-och fel funktioner att tillämpas.
            
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
    
    5. Skicka API-begäran. Lägg till din prenumerations nyckel i `Ocp-Apim-Subscription-Key` rubriken och ange `content-type` till `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Kör programmet och Visa svaret

Det här programmet kan köras i webbläsaren. Ett lyckat svar returneras i JSON-format. Du kan också hitta JSON-svaret på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för REST-API](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)

Ta en titt på följande exempel program på GitHub för att se hur API: et för färg igenkänning fungerar i en digital inkungs app:
* [C# och Universal Windows-plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# och Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webbläsarappen](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- och Android-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- och iOS-mobilapp](https://go.microsoft.com/fwlink/?linkid=2089805)