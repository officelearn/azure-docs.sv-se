---
title: " Skapa en enda sida webbapp - Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Lär dig hur du integrerar Bing Visual Search API i ett enkelsidigt program.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/05/2019
ms.author: aahi
ms.openlocfilehash: 084aad5540a2bd56d98e343639a45c16f786e599
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829607"
---
# <a name="create-a-visual-search-single-page-web-app"></a>Skapa en enda sida i Visual Search webbapp

Bing Visual Search API returnerar insikter för en bild. Du kan ladda upp en bild, eller så kan du ange en URL till en. Insikterna är snarlika bilder, perioder källor, webbsidor som innehåller bilden och mycket mer. Insikter som returneras av Bing Visual Search API liknar de som visas på Bing.com/images.

Den här självstudien beskrivs hur du utökar en enda sida webbapp för sökning i Bing. Om du vill visa självstudien eller hämta källkoden används här, se [självstudien: Skapa en ensidesapp för Bings API för bildsökning](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

Den fullständiga källkoden för det här programmet (när du utökar den för att använda Bing Visual Search API), är tillgänglig på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Anropa API i Bing Visual Search och hantera svaret

Redigera bildsökning i Bing-självstudiekursen och Lägg till följande kod i slutet av den `<script>` element (och före avslutande `</script>` tagg). Följande kod hanterar ett visual search-svar från API: et, upprepas resultatet och visar dem:

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

Följande kod skickar en sökbegäran till API: et, med en händelselyssnare för att anropa `handleVisualSearchResponse()`:

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

Lägg till följande kod till den `searchItemsRenderer` objekt. Den här koden lägger till en **find similar**-länk (sök efter liknande) som anropar `bingVisualSearch`-funktionen när du klickar på den. Funktionen tar emot den `imageInsightsToken` som ett argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Visa liknande bilder

Lägg till följande HTML-kod på rad 601. Den här markup koden lägger till ett element för att visa resultatet av Bing Visual Search API-anropet:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Med all ny JavaScript-kod och HTML-element på plats visas sökresultatet med en **find similar**-länk (sök efter liknande). Klicka på länken för att fylla i avsnittet **Similar** (Liknande) med bilder som liknar dem du valt. Du kan behöva expandera avsnittet **Similar** (Liknande) för att visa bilderna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Beskär en bild med Bing Visual Search SDK förC#](tutorial-visual-search-crop-area-results.md)
