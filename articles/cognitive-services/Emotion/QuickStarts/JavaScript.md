---
title: Känslo-API JavaScript Snabbstart | Microsoft Docs
description: Hämta information och exempel på kod för att snabbt komma igång med Känslo-API med JavaScript i kognitiva Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 2578b0212f9b4a6483402074d7c9eff73e51ca6b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352401"
---
# <a name="emotion-api-javascript-quick-start"></a>Känslo-API JavaScript Snabbstart

> [!IMPORTANT]
> Förhandsgranskning av video API avslutas på 30 oktober 2017. Prova den nya [Video indexeraren API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) enkelt extrahera insikter från videor och förbättra innehållsidentifiering upplevelser, till exempel sökresultat med hjälp av tal, ytor, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med den [Känslo-API känner igen metoden](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med JavaScript för att identifiera emotikoner uttryckt genom en eller flera personer i en bild.

## <a name="prerequisite"></a>Krav
* Hämta din kostnadsfria prenumerationen nyckel [här](https://azure.microsoft.com/try/cognitive-services/), eller om du har en Azure-prenumeration skapa en resurs för Känslo-API och dit din prenumeration nyckel och en slutpunkt.

![Skapa resurs för Känslo-API](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Identifiera emotikoner JavaScript Exempelbegäran

Kopiera följande och spara den till en fil som `test.html`. Ändra begäran `url` att använda den plats där du har köpt din prenumeration nycklar och Ersätt ”Ocp-Apim-prenumeration-Key”-värdet med giltig prenumeration-nyckel. Dessa finns i Azure-portalen i avsnitten översikt och nycklar för din Känslo-API-resurs respektive. 

![API-slutpunkt](../Images/api-url.png)

![API-nyckel för prenumeration](../Images/keys.png)

och ändra begärandetexten till platsen för en bild som du vill använda. Att köra det här exemplet dra och släpp filen i webbläsaren.

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

## <a name="recognize-emotions-sample-response"></a>Identifiera emotikoner Exempelsvar
Lyckade anrop returnerar en matris av framsidan poster och deras associerade känslo poäng rangordnas efter ansikte rektangel storlek i fallande ordning. Ett tomt svar anger att ingen ytor upptäcktes. Känslo-posten innehåller följande fält:
* faceRectangle - placering av ansikte i avbildningen.
* resultat - Place poängen för varje yta i bilden. 

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
