---
title: " Skapa en ensidig webbapp - Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Lär dig hur du integrerar API:et för visuell sökning i Bing i ett ensidigt webbprogram.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.openlocfilehash: 83cdaecfb819fb1f4677b051f87e23e0e03daef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370105"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Självstudiekurs: Skapa en webbsida med ensidig visuell sökning

Api:et för visuell sökning i Bing returnerar insikter för en bild. Du kan antingen ladda upp en bild eller ange en webbadress till en. Insikter är visuellt liknande bilder, shoppingkällor, webbsidor som innehåller bilden med mera. Insikter som returneras av API:et för visuell sökning i Bing liknar dem som visas på Bing.com/images.

I den här självstudien beskrivs hur du utökar en ensidig webbapp för API:et för bildsökning av Bing. Om du vill visa den självstudien eller hämta källkoden som används här läser du [Självstudiekurs: Skapa en ensidig app för API:et för bing-bildsökning](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

Den fullständiga källkoden för det här programmet (efter att ha utökat den till att använda API:et för visuell sökning i Bing) finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Krav

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Anropa API:et för visuell sökning i Bing och hantera svaret

Redigera självstudiekursen Bing Image Search och lägg `<script>` till följande kod `</script>` i slutet av elementet (och före den avslutande taggen). Följande kod hanterar ett visuellt söksvar från API: eterar igenom resultaten och visar dem:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

Följande kod skickar en sökbegäran till API:et `handleVisualSearchResponse()`med hjälp av en händelseavlyssnare för att ringa:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Samla in insikter token

Lägg till följande `searchItemsRenderer` kod i objektet. Den här koden lägger till en **find similar**-länk (sök efter liknande) som anropar `bingVisualSearch`-funktionen när du klickar på den. Funktionen får `imageInsightsToken` som ett argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Visa liknande bilder

Lägg till följande HTML-kod på rad 601. Den här kodifieringen lägger till ett element för att visa resultatet av API-anropet för visuell sökning i Bing:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Med all ny JavaScript-kod och HTML-element på plats visas sökresultatet med en **find similar**-länk (sök efter liknande). Klicka på länken för att fylla i avsnittet **Similar** (Liknande) med bilder som liknar dem du valt. Du kan behöva expandera avsnittet **Similar** (Liknande) för att visa bilderna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Beskär en bild med Bing Visual Search SDK för C #](tutorial-visual-search-crop-area-results.md)
