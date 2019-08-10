---
title: " Bygg en webb program med en enda sida – Visuell sökning i Bing"
titleSuffix: Azure Cognitive Services
description: Lär dig hur du integrerar API för visuell sökning i Bing i ett webb program med en enda sida.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: aahi
ms.openlocfilehash: e0370be1c10bc0f5813bec833be78ad31a3d61a7
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880650"
---
# <a name="create-a-visual-search-single-page-web-app"></a>Skapa en Visuell sökning webb program med en enda sida

API för visuell sökning i Bing returnerar insikter för en bild. Du kan antingen ladda upp en avbildning eller ange en URL till en. Insikter är visuellt likartade bilder, shopping källor, webb sidor som innehåller avbildningen med mera. Insikter som returneras av API för visuell sökning i Bing liknar de som visas på Bing.com/images.

I den här självstudien beskrivs hur du utökar en enskild sidas webbapp för API för bildsökning i Bing. Om du vill visa den själv studie kursen eller hämta käll koden som [används här, se Självstudier: Skapa en app med en sida för API för bildsökning i Bing](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

Den fullständiga käll koden för det här programmet (när den har utökats för att använda API för visuell sökning i Bing) finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Anropa API för visuell sökning i Bing och hantera svaret

Redigera bildsökning i Bing själv studie kursen och Lägg till följande kod i slutet av `<script>` -elementet (och före den avslutande `</script>` taggen). Följande kod hanterar ett visuellt Sök svar från API: et, itererar igenom resultaten och visar dem:

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

Följande kod skickar en Sök förfrågan till API: et med hjälp av en händelse lyssnare som `handleVisualSearchResponse()`anropar:

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

Lägg till följande kod i `searchItemsRenderer` objektet. Den här koden lägger till en **find similar**-länk (sök efter liknande) som anropar `bingVisualSearch`-funktionen när du klickar på den. Funktionen får `imageInsightsToken` som ett argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Visa liknande bilder

Lägg till följande HTML-kod på rad 601. Den här kod posten lägger till ett-element för att visa resultatet av API för visuell sökning i Bing anropet:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Med all ny JavaScript-kod och HTML-element på plats visas sökresultatet med en **find similar**-länk (sök efter liknande). Klicka på länken för att fylla i avsnittet **Similar** (Liknande) med bilder som liknar dem du valt. Du kan behöva expandera avsnittet **Similar** (Liknande) för att visa bilderna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier: Beskär en avbildning med Visuell sökning i Bing SDK förC#](tutorial-visual-search-crop-area-results.md)
