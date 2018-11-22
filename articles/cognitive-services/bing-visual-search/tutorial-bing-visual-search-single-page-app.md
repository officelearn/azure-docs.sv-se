---
title: 'Självstudie: Skapa en webbapp med enda sida – Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Visar hur du använder API för visuell sökning i Bing i ett enkelsidigt webbprogram.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 10/04/2017
ms.author: aahi
ms.openlocfilehash: b493f65e47f5e4c932ed1229e4c00ee1af3cd5ab
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161862"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Självstudie: Visuell sökning med en webbapp med en enda sida

API:t Bing Visual Search ger en upplevelse som liknar avbildningsdetaljerna som visas på Bing.com/images. Med visuell sökning kan du ange en bild och få tillbaka insikter om bilden som visuellt likartade bilder, shoppingkällor, webbsidor som innehåller bilden och mycket mer. 

Den här självstudiekursen kompletterar appen med en sida från självstudien Bildsökning i Bing (se [Enkelsidig webbapp](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Den fullständiga källkoden för att starta den här självstudien finns i [Enkelsidig webbapp (källkod)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Den slutliga källkoden för den här självstudien finns i [Visuell sökning med en webbapp med en enda sida](tutorial-bing-visual-search-single-page-app-source.md).

Här är några av uppgifterna:

> [!div class="checklist"]
> * Anropa API för visuell sökning i Bing med en bildinsiktstoken
> * Visa liknande bilder

## <a name="call-bing-visual-search"></a>Anropa visuell sökning i Bing
Redigera självstudien Bildsökning i Bing och lägg till följande kod i slutet av skriptelementet på rad 409. Den här koden anropar API för visuell sökning i Bing och visar resultatet.

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

## <a name="capture-insights-token"></a>Samla in insikter token
Lägg till följande kod till objektet `searchItemsRenderer` på rad 151. Den här koden lägger till en **find similar**-länk (sök efter liknande) som anropar `bingVisualSearch`-funktionen när du klickar på den. Funktionen tar emot imageInsightsToken som ett argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Visa liknande bilder
Lägg till följande HTML-kod på rad 601. Den här markeringskoden lägger till ett element som används för att visa resultatet för API för Bing Visual Search-anropet.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Med all ny JavaScript-kod och HTML-element på plats visas sökresultatet med en **find similar**-länk (sök efter liknande). Klicka på länken för att fylla i avsnittet **Similar** (Liknande) med bilder som liknar dem du valt. Du kan behöva expandera avsnittet **Similar** (Liknande) för att visa bilderna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Källa för Visuell sökning med en webbapp med en enda sida](tutorial-bing-visual-search-single-page-app-source.md)
> [Referens till API för visuell sökning i Bing](https://aka.ms/bingvisualsearchreferencedoc)