---
title: 'Snabb start: Hämta bild insikter med hjälp av REST API och Node.js-Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Lär dig att ladda upp en avbildning med hjälp av API för visuell sökning i Bing och Node.js och hämta insikter om avbildningen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-js
ms.openlocfilehash: 94a642886b626eb84da3a2d02684b5dd170dcbb1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499075"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Snabb start: Hämta bild insikter med hjälp av Visuell sökning i Bing REST API och Node.js

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Använd den här snabb starten för att göra ditt första anrop till API för visuell sökning i Bing. Det här enkla JavaScript-programmet laddar upp en bild till API:et och visar den information som returneras om den. Även om det här programmet är skrivet i Java Script är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

## <a name="prerequisites"></a>Förutsättningar

* [Node.js](https://nodejs.org/en/download/)
* Modulen för begäran för Java Script. Du kan använda `npm install request` kommandot för att installera modulen.
* Modulen formulär-data. Du kan använda `npm install form-data` kommandot för att installera modulen. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera programmet

1. Skapa en JavaScript-fil i din favorit-IDE eller-redigerare och ange följande krav:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Skapa variabler för din API-slutpunkt, prenumerationsnyckel och sökvägen till din bild. För `baseUri` värdet kan du använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Skapa en funktion `requestCallback()` som heter för att skriva ut svaret från API: et.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Skapa och skicka sökbegäran

1. När du laddar upp en lokal avbildning måste formulär data innehålla `Content-Disposition` sidhuvudet. Ange dess `name` parameter till "image" och ange `filename` parametern till fil namnet för din avbildning. Innehållet i formuläret är en bilds binära data. Den maximala bild storlek som du kan ladda upp är 1 MB.

   ```
   --boundary_1234-abcd
   Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

   ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

   --boundary_1234-abcd--
   ```

2. Skapa ett nytt `FormData` objekt med `FormData()` och Lägg till din avbildnings Sök väg med hjälp av `fs.createReadStream()` .
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

3. Använd biblioteket för begäran för att ladda upp avbildningen och anropa `requestCallback()` för att skriva ut svaret. Lägg till din prenumerations nyckel i begär ande huvudet.

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