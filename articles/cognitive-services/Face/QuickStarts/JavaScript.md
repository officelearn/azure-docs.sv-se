---
title: 'Snabbstart: Identifiera ansikten i en bild med REST API och JavaScript'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten identifierar du ansikten med hjälp av Ansikts-API:et med Javascript i Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.custom: devx-track-js
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: f302000529e0dbf7ecce69ac9bebe77af59561ee
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020848"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-javascript"></a>Snabbstart: Identifiera ansikten i en bild med REST API och JavaScript

I den här snabb starten ska du använda Azures ansikts REST API med Java Script för att identifiera mänskliga ansikten i en bild.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* En kodredigerare som t.ex. [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="initialize-the-html-file"></a>Initiera HTML-filen

Skapa en ny HTML-fil, *detectFaces.html*, och lägg till följande kod.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Detect Faces Sample</title>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
    </head>
    <body></body>
</html>
```

Lägg sedan till följande kod i elementet `body` i dokumentet. Den här koden konfigurerar ett grundläggande användar gränssnitt med ett URL-fält, en **analys ansikte** -knapp, ett svars fönster och ett fönster för bild visning.

:::code language="html" source="~/cognitive-services-quickstart-code/javascript/web/face/rest/detect.html" id="html_include":::

## <a name="write-the-javascript-script"></a>Skriva JavaScript-skriptet

Lägg till följande kod direkt ovanför elementet `h1` i dokumentet. Den här koden konfigurerar den JavaScript-kod som anropar Ansikts-API.

:::code language="html" source="~/cognitive-services-quickstart-code/javascript/web/face/rest/detect.html" id="script_include":::

Du måste uppdatera `subscriptionKey` fältet med värdet för din prenumerations nyckel och du måste ändra `uriBase` strängen så att den innehåller rätt slut punkts sträng. Fältet `returnFaceAttributes` anger vilka ansiktsattribut som ska hämtas. Du kan behöva ändra den här strängen beroende på hur din användning kommer att bli.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>Kör skriptet

Öppna *detectFaces.html* i webbläsaren. När du klickar på knappen **Analyze face** (Analysera ansikte) bör appen visa bilden från den angivna URL:en och skriva ut en JSON-sträng med ansiktsdata.

![GettingStartCSharpScreenshot](../Images/face-detect-javascript.png)

Följande text är ett exempel på ett lyckat JSON-svar.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]
```

## <a name="extract-face-attributes"></a>Extrahera ansikts attribut
 
Om du vill extrahera ansikts attribut använder du identifierings modell 1 och lägger till `returnFaceAttributes` Frågeparametern.

```javascript
// Request parameters.
var params = {
    "detectionModel": "detection_01",
    "returnFaceAttributes": "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise",
    "returnFaceId": "true"
};
```

Svaret innehåller nu ansikts attribut. Exempel:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skrev du ett JavaScript-skript som anropar Azure Face service för att identifiera ansikten i en bild och returnera deras attribut. Läs ansikts-API-referensdokumentationen om du vill veta mer.

> [!div class="nextstepaction"]
> [Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
