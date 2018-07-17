---
title: 'Känslo-API: et JavaScript-Snabbstart | Microsoft Docs'
description: Hämta information och exempel på kod som hjälper dig att snabbt komma igång med Känslo-API med JavaScript i Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: fb9cc2335582c4ec75ec45635e519346d65d7e08
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072100"
---
# <a name="emotion-api-javascript-quick-start"></a>Känslo-API: et JavaScript-Snabbstart

> [!IMPORTANT]
> Förhandsversionen av video API avslutas den 30 oktober 2017. Prova den nya [förhandsversionen av Video Indexer API](https://azure.microsoft.com/services/cognitive-services/video-indexer/) att enkelt lyfta ut kunskaper från videor och förbättra innehållsidentifiering, till exempel sökresultaten genom att identifiera talade ord, ansikten, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med den [Känslo-API: et känner igen metoden](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med JavaScript för att identifiera känslor uttryckt genom en eller flera personer i en bild.

## <a name="prerequisite"></a>Krav
* Få din kostnadsfria Prenumerationsnyckel [här](https://azure.microsoft.com/try/cognitive-services/), eller om du har en Azure-prenumeration skapar en Känslo-API-resurs och dit din Prenumerationsnyckel och slutpunkt.

![Skapa Känslo-API-resursen](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Identifiera känslor JavaScript Exempelbegäran

Kopiera följande och spara den till en fil som `test.html`. Ändra begäran `url` att använda den plats där du har fått din prenumerationsnycklar och Ersätt ”Ocp-Apim-Subscription-Key”-värdet med giltig prenumeration-nyckel. Dessa finns i Azure-portalen i avsnitten översikt och nycklar för din Känslo-API-resurs respektive. 

![API-slutpunkt](../Images/api-url.png)

![API-Prenumerationsnyckel](../Images/keys.png)

Ändra frågans brödtext till platsen för en avbildning som du vill använda. Att köra det här exemplet dra och släpp filen i webbläsaren.

```html
<!DOCTYPE html>
<html>
<head>
    <title>JSSample</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>

<h2>Face Rectangle</h2>
<ul id="faceRectangle">
<!-- Will populate list with response content -->
</ul>

<h2>Emotions</h2>
<ul id="scores">
<!-- Will populate list with response content -->
</ul>

<body>

<script type="text/javascript">
    $(function() {
        // No query string parameters for this API call.
        var params = { };
      
        $.ajax({
            // NOTE: You must use the same location in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            url: "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?" + $.param(params),
            beforeSend: function(xhrObj){
                // Request headers, also supports "application/octet-stream"
                xhrObj.setRequestHeader("Content-Type","application/json");

                // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","<your subscription key>");
            },
            type: "POST",
            // Request body
            data: '{"url": "<your image url>"}',
        }).done(function(data) {
            // Get face rectangle dimensions
            var faceRectangle = data[0].faceRectangle;
            var faceRectangleList = $('#faceRectangle');

            // Append to DOM
            for (var prop in faceRectangle) {
                faceRectangleList.append("<li> " + prop + ": " + faceRectangle[prop] + "</li>");
            }
            
            // Get emotion confidence scores
            var scores = data[0].scores;
            var scoresList = $('#scores');

            // Append to DOM
            for(var prop in scores) {
                scoresList.append("<li> " + prop + ": " + scores[prop] + "</li>")
            }
        }).fail(function(err) {
            alert("Error: " + JSON.stringify(err));
        });
    });
</script>
</body>
</html>
```

## <a name="recognize-emotions-sample-response"></a>Identifiera känslor Exempelsvar
Ett genomfört anrop returnerar en matris med ansikts-poster och deras associerade känslo-resultat, rankade efter ansikte rektangel storlek i fallande ordning. Ett tomt svar anger att ingen ansikten har identifierats. En känslo-post innehåller följande fält:
* faceRectangle - placering av ansikte i bilden.
* resultat – Känslo-resultaten för varje ansikte i bilden. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
