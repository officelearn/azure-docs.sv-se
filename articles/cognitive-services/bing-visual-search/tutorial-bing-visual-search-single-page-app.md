---
title: Webbprogram för Bing Image-sökning sida | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visar hur du använder Bing avbildningen Sök API i en enda sida webbprogram.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-brapel
ms.openlocfilehash: 303d7745167d2ea25fda083ed99881ac4e0a7ec7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354138"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Självstudier: Visual Sök Single-page-Webbapp

Bing Visual Sök API ger en upplevelse som liknar bilden uppgifterna på Bing.com/images. Med Visual Search du anger en avbildning och få tillbaka insikter om avbildningen som visuellt liknande bilder, shopping källor, webbsidor som innehåller bilden och mycket mer. 

Den här självstudiekursen utökar webbprogram sida från Bing Image-sökning kursen (finns [Single-page webbprogrammet](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Fullständig källkod för att starta den här självstudiekursen, se [Single-page webbapp (källkod)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Läs slutlig källkoden för den här självstudiekursen, [Visual Sök Single-page webbprogrammet](tutorial-bing-visual-search-single-page-app-source.md).

Uppgifter som beskrivs är:

> [!div class="checklist"]
> * Anropa API för Visual Sök i Bing med en avbildning insikter token
> * Visa liknande bilder

## <a name="call-bing-visual-search"></a>Anropa Bing Visual sökning
Redigera kursen Bing Image-sökning och Lägg till följande kod i slutet av elementet skript på rad 409. Den här koden Bing-API: n för Visual Sök och visar resultatet.

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

## <a name="capture-insights-token"></a>Avbilda insikter token
Lägg till följande kod i den `searchItemsRenderer` objektet vid rad 151. Den här koden lägger till en **hitta liknande** länk som anropar den `bingVisualSearch` fungera när du klickar på. Funktionen får imageInsightsToken som ett argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Visa liknande bilder
Lägg till följande HTML-kod på rad 601. Den här koden markup lägger till ett element som används för att visa resultatet av Bing Visual Sök-API-anrop.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Med alla nya JavaScript-kod och HTML-element för sökresultat visas med en **hitta liknande** länk. Klicka på länken för att fylla i **liknar** avsnitt med bilder som liknar det du valt. Du kan behöva expandera den **liknar** avsnittet bilderna ska visas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visual Sök Single-page app webbadress](tutorial-bing-visual-search-single-page-app-source.md)
> [Bing Visual Sök API-referens](https://aka.ms/bingvisualsearchreferencedoc)