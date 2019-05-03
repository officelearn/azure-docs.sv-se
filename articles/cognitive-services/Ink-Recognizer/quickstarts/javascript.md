---
title: 'Snabbstart: Identifiera digitala ink med Ink Igenkännande REST API och Node.js'
description: Använd Ink Igenkännande API för att starta eftersom digitala ink linjer.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: febc6e72ed40541a230c606a3ec96a8bffef5036
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026383"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Snabbstart: Identifiera digitala ink med Ink Igenkännande REST API och JavaScript

Använd den här snabbstarten för att börja använda API: et för pennanteckningar Igenkännande på digitala ink linjer. Det här programmet JavaScript skickar en API-begäran som innehåller JSON-formaterade ink linje data och visar svaret.

Även om det här programmet är skriven i Javascript och körs i webbläsaren, är API: et en RESTful-webb-tjänst som är kompatibla med de flesta programmeringsspråk.

Vanligtvis skulle du anropa API från en digital digital penna app. Den här snabbstarten skickar ink linje data för följande handskriven exempel från en JSON-fil.

![en bild av handskriven text](../media/handwriting-sample.jpg)

Källkoden för den här snabbstarten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En webbläsare
- Exempel ink linje data för den här snabbstarten finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Skapa ett nytt program

1. I din favorit-IDE eller redigerare, skapar en ny `.html` fil. Lägg sedan till grundläggande HTML till den kod som vi lägger till senare.
    
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

2. I den `<body>` tagga, Lägg till följande html:
    1. Två textområden för att visa JSON-begäran och svaret.
    2. En knapp för att anropa den `recognizeInk()` funktion som skapas senare.
    
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

## <a name="load-the-example-json-data"></a>Läsa in exempel JSON-data

1. I den `<script>` tagga, skapa en variabel för sampleJson. Skapa en JavaScript-funktion med namnet `openFile()` som öppnar en Utforskaren, så du kan välja en JSON-fil. När den `Recognize ink` klickar på knappen, den anropar funktionen och börja läsa filen.
2. Använd en `FileReader` objektets `onload()` funktionen för att bearbeta filen asynkront. 
    1. Ersätt alla `\n` eller `\r` tecken i filen med en tom sträng. 
    2. Använd `JSON.parse()` att konvertera texten till giltig JSON
    3. Uppdatera den `request` textrutan i programmet. Använd `JSON.stringify()` att formatera JSON-sträng. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Skicka en begäran till Ink Igenkännande API

1. I den `<script>` tagga, skapa en funktion som kallas `recognizeInk()`. Den här funktionen kommer senare att anropa API och uppdatera sidan med svaret. Lägg till koden från följande steg i den här funktionen. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Skapa variabler för slutpunkts-URL, prenumerationsnyckel och exemplet-JSON. Skapa sedan en `XMLHttpRequest` -objektet skickar API-begäran. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Skapa returfunktionen för den `XMLHttpRequest` objekt. Den här funktionen Analysera API-svaret från en lyckad begäran och visa dem i programmet. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Skapa felfunktionen för Begäranobjektet. Den här funktionen loggas felet i konsolen. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Skapa en funktion för Begäranobjektet `onreadystatechange` egenskapen. När begäran objektets beredskapstillståndet ändras, används dessa funktioner för återlämning och fel.
            
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
    
    5. Skicka API-begäran. Lägg till din prenumerationsnyckel till den `Ocp-Apim-Subscription-Key` rubrik och ange den `content-type` till `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };

## Run the application and view the response

This application can be run within your web browser. A successful response is returned in JSON format. You can also find the JSON response on [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502):

## Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://go.microsoft.com/fwlink/?linkid=2089907)

To see how the Ink Recognition API works in a digital inking app, take a look at the following sample applications on GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# and Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript web-browser app](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java and Android mobile app](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift and iOS mobile app](https://go.microsoft.com/fwlink/?linkid=2089805)
