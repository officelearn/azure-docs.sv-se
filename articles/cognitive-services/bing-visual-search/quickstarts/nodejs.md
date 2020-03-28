---
title: 'Snabbstart: Hämta bildinsikter med REST API och Node.js - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar upp en bild till API:et för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 373d6fa5402ba703cbebe88ad562974ba97f3391
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379716"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Snabbstart: Hämta bildinsikter med hjälp av Bing Visual Search REST API och Node.js

Använd den här snabbstarten för att skicka ditt första anrop till API:et för visuell sökning i Bing och visa sökresultaten. Det här enkla JavaScript-programmet laddar upp en bild till API:et och visar den information som returneras om den. Även om det här programmet är skrivet i JavaScript är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Krav

* [Node.js](https://nodejs.org/en/download/)
* Modulen Begäran för JavaScript. Du kan `npm install request` använda kommandot för att installera modulen.
* Formulärdatamodulen. Du kan `npm install form-data` använda kommandot för att installera modulen. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera programmet

1. Skapa en JavaScript-fil i din favorit-IDE eller redigerare och ange följande krav:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Skapa variabler för din API-slutpunkt, prenumerationsnyckel och sökvägen till din bild. `baseUri`kan vara den globala slutpunkten nedan eller den [anpassade underdomänslutpunkten](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure-portalen för din resurs:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Skapa en `requestCallback()` funktion som heter för att skriva ut svaret från API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Skapa och skicka sökbegäran

När du överför en lokal bild måste `Content-Disposition` formulärdata innehålla sidhuvudet. Du måste `name` ange parametern till "image", och parametern `filename` kan ställas in på valfri sträng. Innehållet i formuläret innehåller den binära data i bilden. Den maximala bildstorlek som du kan ladda upp är 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

1. Skapa ett nytt **FormData-objekt** med `FormData()`och lägg till `fs.createReadStream()`bildsökvägen i det med hjälp av:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Använd förfrårebiblioteket för `requestCallback()` att ladda upp bilden och anropa för att skriva ut svaret. Var noga med att lägga till din prenumerationsnyckel i förfrågningshuvudet:

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ensidig visuell sökapp](../tutorial-bing-visual-search-single-page-app.md)
