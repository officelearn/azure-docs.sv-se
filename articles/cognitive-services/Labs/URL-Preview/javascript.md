---
title: 'Snabbstart: Förhandsversion av projekt-URL, JavaScript'
titlesuffix: Azure Cognitive Services
description: Skriptexempel för att snabbt komma igång med API för URL-förhandsgranskning i Bing med JavaScript.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 1f3c37e770c2edd76bd299771648b2de29d42dd9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873656"
---
# <a name="quickstart-url-preview-in-javascript"></a>Snabbstart: URL-förhandsgranskning i JavaScript 

Följande enkelsidiga program använder JavaScript för att skapa en URL-förhandsgranskning för webbplatsen SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Få en åtkomstnyckel för den kostnadsfria utvärderingsversionen av [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview)

## <a name="code-scenario"></a>Kodscenario
Följande JavaScript-exempel innehåller ett indataobjekt som är en textruta, där användaren anger URL:en som ska förhandsgranskas.  När användaren klickar på knappen **Preview** (Förhandsgranska) dirigerar onclick-metoden till `getPreview` där kod genererar en webbegäran till **UrlPreview**-slutpunkten.

Koden skapar en *XMLHttpRequest*, lägger till huvudet och nyckeln *Ocp-Apim-Subscription-Key* och skickar begäran.  En asynkron händelsehanterare läggs till för att bearbeta svaret.

Om svaret returneras tilldelar hanteraren JSON-texten för svaret till `demo`-stycket på sidan. Andra svarselement ställs in på följande stycken för visning.

**Råsvar från JSON**

```
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

```

**Demo som körs**

![Exempel på URL-förhandsgranskning i JavaScript](./media/java-script-demo.png)

## <a name="running-the-application"></a>Köra programmet

Så här kör du programmet:

1. Ersätt värdet `YOUR-SUBSCRIPTION-KEY` med en giltig åtkomstnyckel för din prenumeration.
2. Spara HTML och skript i en fil med tillägget .html.
3. Kör webbsida i en webbläsare.
4. Använd den befintliga URL:en eller ange en annan i textrutan.
5. Klicka på knappen **Preview** (Förhandsgranska).

**Källkod:**

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
- [Snabbstart för Node](node-quickstart.md)
- [Snabbstart för Python](python-quickstart.md)
