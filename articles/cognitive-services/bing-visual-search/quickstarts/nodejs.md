---
title: 'Snabb start: Hämta bild insikter med hjälp av REST API och Node. js-Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar du upp en bild till API för visuell sökning i Bing och får information om den.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: ecfe341fa050e693f919f35c29c8120c687c88f8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383197"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Snabb start: Hämta bild insikter med hjälp av Visuell sökning i Bing REST API och Node. js

Använd den här snabbstarten för att skicka ditt första anrop till API för visuell sökning i Bing och visa sökresultaten. Det här enkla JavaScript-programmet laddar upp en bild till API:et och visar den information som returneras om den. Även om det här programmet är skrivet i JavaScript, är API:n en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

När du laddar upp en lokal avbildning måste formulär data innehålla `Content-Disposition`s huvudet. Du måste ange dess `name` parameter till "bild" och parametern `filename` kan anges till valfri sträng. Innehållet i formuläret är en bilds binära data. Den maximala bildstorlek som du kan ladda upp är 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Krav

* [Node.js](https://nodejs.org/en/download/)
* Modulen för begäran för Java Script. Du kan använda kommandot `npm install request` för att installera modulen.
* Modulen formulär-data. Du kan använda kommandot `npm install form-data` för att installera modulen. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera appen

1. Skapa en JavaScript-fil i din favorit-IDE eller-redigerare och ange följande krav:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Skapa variabler för API-slutpunkten, prenumerations nyckeln och sökvägen till din avbildning:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Skapa en funktion med namnet `requestCallback()` för att skriva ut svaret från API: et:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Skapa och skicka sökbegäran

1. Skapa ett nytt **formdata** -objekt med `FormData()`och Lägg till sökvägen till din avbildning med hjälp av `fs.createReadStream()`:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Använd biblioteket för begäran för att ladda upp avbildningen och anropa `requestCallback()` för att skriva ut svaret. Se till att lägga till din prenumerations nyckel i begär ande huvudet:

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
> [Bygg en Visuell sökning webb program med en enda sida](../tutorial-bing-visual-search-single-page-app.md)
