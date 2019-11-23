---
title: 'Quickstart: Get image insights using the REST API and Node.js - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar upp en bild till API:et för visuell sökning i Bing och får information om den.
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
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Quickstart: Get image insights using the Bing Visual Search REST API and Node.js

Använd den här snabbstarten för att skicka ditt första anrop till API:et för visuell sökning i Bing och visa sökresultaten. Det här enkla JavaScript-programmet laddar upp en bild till API:et och visar den information som returneras om den. Även om det här programmet är skrivet i JavaScript är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

When uploading a local image, the form data must include the `Content-Disposition` header. You must set its `name` parameter to "image", and the `filename` parameter can be set to any string. The contents of the form include the binary data of the image. Den maximala bildstorlek som du kan ladda upp är 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Krav

* [Node.js](https://nodejs.org/en/download/)
* The Request module for JavaScript. You can use `npm install request` command to install the module.
* The form-data module. You can use the `npm install form-data` command to install the module. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Initiera programmet

1. Create a JavaScript file in your favorite IDE or editor, and set the following requirements:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Create variables for your API endpoint, subscription key, and the path to your image:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Create a function named `requestCallback()` to print the response from the API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Skapa och skicka sökbegäran

1. Create a new **FormData** object using `FormData()`, and append your image path to it, using `fs.createReadStream()`:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Use the request library to upload the image, and call `requestCallback()` to print the response. Be sure to add your subscription key to the request header:

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
> [Build a Visual Search single-page web app](../tutorial-bing-visual-search-single-page-app.md)
