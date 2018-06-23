---
title: JavaScript-Snabbstart för Project URL Preview - kognitiva Microsoft-tjänster | Microsoft Docs
description: Skriptexempel att snabbt komma igång med Bing URL Preview API i Microsoft kognitiva Services på Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9041a88a292fb2dabead69195ebc3074e2ecf486
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354024"
---
# <a name="url-preview-in-javascript"></a>URL: en förhandsgranskning i JavaScript 

Följande sida program använder JavaScript för att skapa en förhandsgranskning för URL: en för webbplatsen SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Förutsättningar

Hämta en åtkomstnyckel för den kostnadsfria utvärderingsversionen [kognitiva Services Labs](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Koden scenario
I följande javascript-exempel innehåller en textruta indataobjektet där användaren anger URL: en för förhandsgranskning.  När användaren klickar på **Preview** knappen onclick metoden vägarna till `getPreview` där kod genereras en webbegäran till den **UrlPreview** slutpunkt.

Koden som skapar en *XMLHttpRequest*, lägger till den *Ocp-Apim-prenumeration-nyckeln* sidhuvud och nyckel, och skickar en begäran.  Det lägger till en asynkron händelsehanterare för att bearbeta svaret.

Om svaret returnerar har hanteraren tilldelar JSON-texten för svar på de `demo` punkt på sidan. Andra element i svaret är inställda på följande punkter för visning.

**Rådata JSON-svar**

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

**Körs demo**

![Förhandsgranskning av JavaScript-Url-exempel](./media/java-script-demo.png)

## <a name="running-the-application"></a>Köra programmet

Att köra programmet:

1. Ersätt den `YOUR-SUBSCRIPTION-KEY` värde med en giltig åtkomstnyckeln för din prenumeration.
2. Spara HTML och skript till en fil med tillägget .html.
3. Kör webbsidan i en webbläsare.
4. Använd den befintliga URL eller ange en annan i textrutan.
5. Klicka på den **Preview** knappen.

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
- [C#-Snabbstart](csharp.md)
- [Java-Snabbstart](java-quickstart.md)
- [Noden Snabbstart](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)