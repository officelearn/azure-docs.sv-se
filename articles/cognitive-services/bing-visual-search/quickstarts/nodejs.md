---
title: JavaScript-Snabbstart för API för Bing Visual Search | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visar hur du överför en bild till Bing Visual Search-API och få tillbaka insikter om avbildningen.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 60b1dc9b8ea9eda258e9776b8967df38c97d964e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071711"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>Din första Bing Visual Search-fråga i JavaScript

Bing Visual Search API returnerar information om en avbildning som du anger. Du kan ange avbildningen med hjälp av URL till bild, ett insights token, eller genom att överföra en avbildning. Information om alternativen finns i [vad är Bing Visual Search API?](../overview.md) Den här artikeln visar att ladda upp en avbildning. Ladda upp en avbildning kan vara användbart i mobila scenarier där du kan ta en bild av en välkänd landmärken och få tillbaka information om den. Insikterna kan exempelvis omfatta kunskap om landmärken. 

Om du laddar upp en lokal avbildning visas nedan formulärdata måste du inkludera i brödtexten i INLÄGGET. Formulärdata måste innehålla Content-Disposition-huvudet. Dess `name` parametern måste anges till ”bild” och `filename` parameter kan anges till valfri sträng. Innehållet i formuläret är den binära filen på avbildningen. Maximal avbildningens storlek kan du överföra är 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Den här artikeln innehåller ett enkelt konsolprogram som skickar en begäran om Bing Visual Search-API och visar JSON-sökresultat. Det här programmet är skriven i JavaScript är API: et en RESTful-webb-tjänst som är kompatibel med alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver [Node.js 6](https://nodejs.org/en/download/) att köra den här koden.

Den här snabbstarten kan du använda en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) prenumerationsnyckel eller en betald prenumeration-nyckel.

## <a name="running-the-application"></a>Köra programmet

Nedan visas hur du skickar meddelandet med FormData i Node.js.

Följ dessa steg om du vill köra det här programmet:

1. Skapa en mapp för ditt projekt (eller Använd din favorit-IDE eller redigerare).
2. Navigera till mappen som du nyss skapade från en kommandotolk eller terminal.
3. Installera modulerna för begäran:  
  ```  
  npm install request  
  ```  
3. Installera modulerna formulärdata:  
  ```  
  npm install form-data  
  ```  
4. Skapa en fil med namnet GetVisualInsights.js och Lägg till följande kod.
5. Ersätt den `subscriptionKey` värdet med din prenumerationsnyckel.
6. Ersätt den `imagePath` värdet med sökvägen för att ladda upp avbildningen.
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

[Få insyn om en avbildning med hjälp av en token för insikter](../use-insights-token.md)  
[Bing Visual Search bild uppladdning självstudien](../tutorial-visual-search-image-upload.md)
[Bing Visual Search-självstudiekursen som ensidesapp](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search-översikt](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Hämta en kostnadsfri utvärderingsversion åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API-referens](https://aka.ms/bingvisualsearchreferencedoc)
