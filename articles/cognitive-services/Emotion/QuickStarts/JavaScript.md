---
title: 'Snabbstart: Känsloigenkänning i ansikten i en bild – Känslo-API, JavaScript'
titlesuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med Känslo-API med JavaScript.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: eeaf2ea080d8c0b604b9831532028e31b8306169
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239497"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Snabbstart: Bygga en app för känsloigenkänning i ansikten i en bild.

> [!IMPORTANT]
> Känslo-API:et kommer att bli inaktuellt den 15 februari 2019. Funktionen för känsloigenkänning är nu allmänt tillgänglig som en del av [Ansikts-API:et](https://docs.microsoft.com/azure/cognitive-services/face/). 

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med hjälp av [Känslo-API:et metoden Recognize](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) med JavaScript för att känna igen känslor som uttrycks av en eller flera personer i en bild.

## <a name="prerequisite"></a>Krav
* Få din kostnadsfria prenumerationsnyckel [här](https://azure.microsoft.com/try/cognitive-services/) eller, om du har en Azure-prenumeration, skapa en Känslo-API-resurs och få din prenumerationsnyckel och slutpunkt där.

![Skapa en resurs för Känslo-API](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Exempelbegäran för känsloigenkänning med JavaScript

Kopiera följande och spara det till en fil såsom `test.html`. Ändra `url`-begäran för att använda platsen där du fick dina prenumerationsnycklar, och ersätt värdet ”Ocp-Apim-Subscription-Key” med din giltiga prenumerationsnyckel. Dessa finns i Azure-portalen i avsnitten Översikt och Nycklar för respektive Känslo-API-resurs.

![API-slutpunkt](../Images/api-url.png)

![API-prenumerationsnyckel](../Images/keys.png)

Ändra begärandetexten till platsen för en bild som du vill använda. Dra och släpp filen i webbläsaren för att köra exemplet.

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

## <a name="recognize-emotions-sample-response"></a>Exempelsvar för känsloigenkänning
Ett genomfört anrop returnerar en matris med ansiktsposter och deras associerade känsloresultat, rankade efter ansiktsrektangelns storlek i fallande ordning. Ett tomt svar indikerar att inga ansikten kändes igen. En känslopost innehåller följande fält:
* faceRectangle – ansiktsrektangelns placering i bilden.
* scores – känsloresultat för varje ansikte i bilden.

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
