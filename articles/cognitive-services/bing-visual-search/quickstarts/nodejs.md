---
title: JavaScript-Snabbstart för Bing Visual sökning API | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visar hur du överför en bild till Bing Visual Sök-API och få tillbaka insikter om bilden.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: dd28c829d8d24980a746244dc6aca880d2d69224
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355092"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>Första Bing Visual sökfrågan i JavaScript

Bing Visual Sök API returnerar information om en avbildning som du anger. Du kan ange avbildningen med hjälp av URL-Adressen till bilden, en insikter token, eller genom att ladda upp en bild. Information om dessa alternativ finns [vad är Bing Visual Sök API?](../overview.md) Den här artikeln visar ladda upp en bild. Ladda upp en bild kan vara användbart i mobila scenarier där du kan ta en bild av en välkänd Landmärke och få tillbaka information om den. Insikter kan exempelvis omfatta kunskap om Landmärke. 

Om du överför en lokal image visas nedan formulärdata måste du inkludera i brödtexten i INLÄGGET. Formulärdata måste innehålla rubriken Content-Disposition. Dess `name` parametern måste anges till ”bild” och `filename` parameter kan anges till en sträng. Innehållet i formuläret är binär för bilden. Du kan ladda upp maximala bildstorleken är 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Den här artikeln innehåller ett enkelt konsolprogram som skickar en begäran i Bing Visual Sök-API och visar sökresultatet JSON. När det här programmet är skriven i JavaScript, är API: et en RESTful webbtjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Node.js 6](https://nodejs.org/en/download/) att köra den här koden.

Du kan använda för Snabbstart, en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) prenumeration nyckeln eller en betald prenumeration nyckel.

## <a name="running-the-application"></a>Köra programmet

Nedan visas hur du skickar meddelandet med FormData i Node.js.

Följ dessa steg om du vill köra det här programmet:

1. Skapa en mapp för ditt projekt (eller Använd din favorit IDE eller redigeraren).
2. Navigera till mappen som du skapade från en kommandotolk eller terminal.
3. Installera modulerna för begäran:  
  ```  
  npm install request  
  ```  
3. Installera formulärdata modulerna:  
  ```  
  npm install form-data  
  ```  
4. Skapa en fil med namnet GetVisualInsights.js och Lägg till följande kod.
5. Ersätt den `subscriptionKey` värdet med din prenumeration nyckel.
6. Ersätt den `imagePath` värdet med sökvägen till bilden som ska överföras.
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

[Få insikter om en avbildning med hjälp av en insights-token](../use-insights-token.md)  
[Bing Visual Sök sida app självstudiekursen](../tutorial-bing-visual-search-single-page-app.md)  
[Översikt över Bing Visual sökning](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Hämta en kostnadsfri utvärderingsversion snabbtangent](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Sök API-referens](https://aka.ms/bingvisualsearchreferencedoc)
