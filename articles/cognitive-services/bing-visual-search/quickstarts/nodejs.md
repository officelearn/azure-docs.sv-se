---
title: 'Snabbstart: Hämta information om bilder med hjälp av REST API för visuell sökning i Bing och Node.js'
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
ms.openlocfilehash: 9414bac220d928618b403aa2f7df7748772e0e9a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047576"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Snabbstart: Hämta information om bilder med hjälp av REST API för visuell sökning i Bing och Node.js

Använd den här snabbstarten för att skicka ditt första anrop till API för visuell sökning i Bing och visa sökresultaten. Det här enkla JavaScript-programmet laddar upp en bild till API:et och visar den information som returneras om den. Även om det här programmet är skrivet i JavaScript är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

När du laddar upp en lokal avbildning formulärdata måste innehålla den `Content-Disposition` rubrik. Du måste ange dess `name` parameter ”bild” och `filename` parameter kan anges till valfri sträng. Innehållet i formuläret inkludera binära data för avbildningen. Den maximala bildstorlek som du kan ladda upp är 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Node.js](https://nodejs.org/en/download/)
* Begäran-modul för JavaScript. Du kan använda `npm install request` kommando för att installera modulen.
* Formulärdata-modul. Du kan använda den `npm install form-data` kommando för att installera modulen. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera programmet

1. Skapa en JavaScript-fil i din favorit-IDE eller redigerare och ange följande krav:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Skapa variabler för din API-slutpunkt, prenumerationsnyckel och sökvägen till din avbildning:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Skapa en funktion med namnet `requestCallback()` ska skrivas ut från API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Skapa och skicka sökbegäran

1. Skapa en ny **FormData** objekt med hjälp av `FormData()`, och lägger till din sökväg till bild, med hjälp av `fs.createReadStream()`:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Använd begäran-biblioteket för att ladda upp avbildningen, och anropa `requestCallback()` ska skrivas ut. Tänk på att lägga till din prenumerationsnyckel i huvudet för begäran:

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
> [Skapa en enda sida i Visual Search webbapp](../tutorial-bing-visual-search-single-page-app.md)
