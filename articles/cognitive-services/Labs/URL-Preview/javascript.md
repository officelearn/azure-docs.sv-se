---
title: JavaScript-Snabbstart för projektet URL-förhandsgranskning – Microsoft Cognitive Services | Microsoft Docs
description: Skriptexempel för att snabbt komma igång med API för Bing URL-förhandsgranskning i Microsoft Cognitive Services på Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: dda6f7c105dfbadc3c22f0c008aa8759fe12fa03
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301360"
---
# <a name="url-preview-in-javascript"></a>URL-förhandsgranskning i JavaScript 

Följande enkelsidigt program använder JavaScript för att skapa en URL-förhandsgranskning för webbplatsen SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Förutsättningar

Hämta en åtkomstnyckel för den kostnadsfria utvärderingsversionen [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview)

## <a name="code-scenario"></a>Kod scenario
I följande javascript-exempel innehåller en textruta indataobjektet där användaren anger URL: en för att förhandsgranska.  När användaren klickar på **förhandsversion** knappen onclick metoden vägar till `getPreview` där koden genererar en webbegäran till den **UrlPreview** slutpunkt.

Koden skapar en *XMLHttpRequest*, lägger till den *Ocp-Apim-Subscription-Key* rubrik och nyckel, och skickar en begäran.  Den lägger till en asynkron händelsehanterare för att bearbeta svaret.

Om svaret returnerar har, hanteraren tilldelar JSON-texten i svaret på den `demo` punkt på sidan. Andra element i svaret är inställda på följande punkter för visning.

**Rå JSON-svar**

````
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

**Köra demo**

![Url-förhandsgranskning för JavaScript-exempel](./media/java-script-demo.png)

## <a name="running-the-application"></a>Köra programmet

Att köra programmet:

1. Ersätt den `YOUR-SUBSCRIPTION-KEY` värde med en giltig åtkomstnyckel för din prenumeration.
2. Spara HTML och skript till en fil med .html-tillägget.
3. Köra webbsidan i en webbläsare.
4. Använd den befintliga URL: en, eller ange en annan i textrutan.
5. Klicka på den **förhandsversion** knappen.

**Källkoden:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](csharp.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för noden](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)
