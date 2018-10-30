---
title: 'Snabbstart: Identifiera ansikten i en bild med hjälp av REST API och cURL'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten identifierar du ansikten med hjälp av Ansikts-API:et med cURL.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: pafarley
ms.openlocfilehash: ab403ec6a9db4d1a0dc03074044eeb424e4ba875
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953356"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-curl"></a>Snabbstart: Identifiera ansikten i en bild med hjälp av REST API och cURL

I den här snabbstarten identifierar du ansikten i en bild med hjälp av Ansikts-API.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en prenumerationsnyckel för att köra exemplet. Du kan hämta nycklar för kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

Använd metoden [Face - Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Ansiktsigenkänning) för att identifiera ansikten i en bild och returnera ansiktsattribut som:

* Ansikts-ID: unikt ID som används i flera Ansikts-API-scenarier.
* Ansiktsrektangel: vänster, överkant, bredd och höjd som anger ansiktets placering i bilden.
* Landmärke: en matris med ansiktslandmärken med 27 punkter som pekar på viktiga positioner för ansiktsdelar.
* Ansiktsattribut som ålder, kön, leendeintensitet, huvudställning och ansiktsbehåring.

För att köra exemplet följer du dessa steg:

1. Öppna en kommandotolk.
2. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
3. Ändra webbadressen (`https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect`) till att använda den plats där du fått dina prenumerationsnycklar om det behövs.
4. Du kan också ändra bilden (`"{\"url\":...`) som ska analyseras om du vill.
5. Klistra in koden i kommandofönstret.
6. Kör kommandot.

### <a name="face---detect-request"></a>Begäran om ansiktsigenkänning

> [!NOTE]
> Du måste använda samma plats i REST-anropet som du använde för att hämta prenumerationsnycklarna. Om du till exempel fått dina prenumerationsnycklar från regionen westus (USA, västra), ersätter du ”westcentralus” i webbadressen nedan med ”westus”.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="face---detect-response"></a>Svar från ansiktsigenkänning

Ett svar som anger att åtgärden lyckades returneras i JSON.

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

Utforska de ansikts-API:er som används för att identifiera ansikten i en bild, avgränsa ansikten med rektanglar och returnera attribut som ålder och kön.

> [!div class="nextstepaction"]
> [Ansikts-API:er](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
