---
title: 'Snabbstart: Skapa en visuell sökfråga, Node.js – Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Så här laddar du upp en bild till API för visuell sökning i Bing och får tillbaka information om bilden.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: b13738c5bfd8fc75224bf934ae8be56e7c2edd69
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225505"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-javascript"></a>Snabbstart: Din första fråga i Visuell sökning i Bing i JavaScript

API för visuell sökning i Bing returnerar information om en bild som du anger. Du kan ange bilden med hjälp av dess URL, en insiktstoken eller genom att ladda upp en bild. Information om alternativen finns i [Vad är API för visuell sökning i Bing?](../overview.md) Den här artikeln visar hur du laddar upp en bild. Att ladda upp en bild kan vara användbart i mobila scenarier, där du kan ta en bild av ett välkänt landmärke och få tillbaka information om det. Informationen kan exempelvis vara fakta om landmärket. 

Om du laddar upp en lokal bild måste du inkludera de formulärdata som visas nedan i brödtexten i POST. Formulärdatan måste innehålla rubriken för innehållsdispositionen. Parametern `name` måste anges till ”image” och parametern `filename` kan anges till valfri sträng. Innehållet i formuläret är binärt för bilden. Den maximala bildstorlek som du kan ladda upp är 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Artikeln innehåller ett enkelt konsolprogram som skickar en begäran till API för visuell sökning i Bing och visar JSON-sökresultatet. Även om det här programmet är skrivet i JavaScript, är API:n är en RESTful-webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Node.js 6](https://nodejs.org/en/download/) för att kunna köra den här koden.

I snabbstarten kan du använda en prenumerationsnyckel för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) eller en betald prenumerationsnyckel.

## <a name="running-the-application"></a>Köra programmet

Nedan visas hur du skickar meddelandet med FormData i Node.js.

Följ dessa steg om du vill köra programmet:

1. Skapa en mapp för ditt projekt (eller använd din favorit-IDE eller redigerare).
2. Gå till mappen som du nyss skapade från en kommandotolk eller terminal.
3. Installera de begärda modulerna:  
  ```  
  npm install request  
  ```  
3. Installera modulerna för formulärdata:  
  ```  
  npm install form-data  
  ```  
4. Skapa en fil med namnet GetVisualInsights.js och lägg till nedanstående kod i den.
5. Ersätt värdet `subscriptionKey` med din prenumerationsnyckel.
6. Ersätt värdet `imagePath` med sökvägen till bilden som ska laddas upp.
7. Kör programmet.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>Nästa steg

[Få information om en bild med hjälp av en insiktstoken](../use-insights-token.md)  
[Självstudie om bilduppladdning i Visuell sökning i Bing](../tutorial-visual-search-image-upload.md)
[Självstudie om ensidesapplikationer i Visuell sökning i Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Översikt för Visuell sökning i Bing](../overview.md)  
[Prova!](https://aka.ms/bingvisualsearchtryforfree)  
[Skaffa en åtkomstnyckel för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referens till API för visuell sökning i Bing](https://aka.ms/bingvisualsearchreferencedoc)
