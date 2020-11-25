---
title: 'Snabbstart: Identifiera ansikten i en bild med Azure REST API och Node.js'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda Azure ansikts-REST API med Node.js för att identifiera ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: a245dae4405706c37b3c142963d325925d089830
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96001447"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-nodejs"></a>Snabbstart: Identifiera ansikten i en bild med ansikts-REST API och Node.js

I den här snabb starten ska du använda Azures ansikts REST API med Node.js för att identifiera mänskliga ansikten i en bild.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
- En kodredigerare som t.ex. [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="set-up-the-node-environment"></a>Konfigurera nodmiljön

Gå till mappen där du vill skapa projektet och skapa den nya filen *facedetection.js*. Installera sedan modulen `axios` i projektet. Det innebär att dina skript kan göra HTTP-begäranden.

```shell
npm install axios --save
```

## <a name="write-the-nodejs-script"></a>Skriv Node.js-skriptet

Klistra in följande kod i *facedetection.js*. Dessa fält anger hur du ansluter till ansiktsigenkänningstjänsten och var du hämtar dess indata. [Skapa miljövariabler](../../cognitive-services-apis-create-account.md?tabs=multiservice%252cwindows#configure-an-environment-variable-for-authentication) och Lägg till din prenumerations nyckel och slut punkt för ditt ansikte. Du kanske vill ändra fältet `imageUrl` så att det pekar på din egen inmatade bild.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/Face/rest/detect.js" id="environment":::

Lägg sedan till följande kod för att anropa ansikts-API:et och hämta ansiktsattribut från den inmatade bilden. I fältet `returnFaceAttributes` anges vilka ansiktsattribut som ska hämtas. Du kanske vill ändra den här strängen beroende på den avsedda användningen.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/Face/rest/detect.js" id="main":::

## <a name="save-and-run-the-script"></a>Spara och kör skriptet

När du har gjort dina ändringar öppnar du en kommandotolk och kör filen med kommandot `node`.

```
node facedetection.js
```

Ett lyckat svar visar ansiktsinformation i lättläst JSON-format. Exempel:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
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
 
Om du vill extrahera ansikts attribut använder du identifierings modell 1 och lägger till `returnFaceAttributes` Frågeparametern. Redigera parametrarna enligt följande:

```javascript
    params : {
        detectionModel: 'detection_01',
        returnFaceAttributes: 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise',
        returnFaceId: true
    },
```

Svaret innehåller nu ansikts attribut. Exempel:

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skrev du ett Node.js-skript som anropar Azure Face service för att identifiera ansikten i en bild och returnera deras attribut. Läs ansikts-API-referensdokumentationen om du vill veta mer.

> [!div class="nextstepaction"]
> [Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 