---
title: 'Snabbstart: Identifiera ansikten i en bild med Azure REST API och cURL'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda Azure ansikts-REST API med cURL för att identifiera ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: b6c05382a3a40aa9157fd64a7f0192c58e79b0ee
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853786"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Snabbstart: Identifiera ansikten i en bild med hjälp av ansikts-REST API och cURL

I den här snabbstarten ska du använda Azure ansikts-REST API med cURL för att identifiera mänskliga ansikten i en bild.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- En ansikts-API-prenumerationsnyckel. Du kan hämta nycklar för en kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på tjänsten Ansikts-API och få din nyckel.

## <a name="write-the-command"></a>Skriva kommandot
 
Du kommer att använda ett kommando som följande för att anropa ansikts-API:et och hämta ansiktsattributdata från en bild. Kopiera först koden till ett redigeringsprogram&mdash;du måste göra ändringar av vissa delar av kommandot innan du kan köra den.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Prenumerationsnyckel
Ersätt `<Subscription Key>` med en giltig ansiktsprenumerationsnyckel.

### <a name="face-endpoint-url"></a>Ansiktsslutpunktens webbadress

Webbadressen `https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect` anger Azure-ansiktsslutpunkten för att fråga. Du kan behöva ändra den första delen av URL:en så att den matchar den region som motsvarar din prenumerationsnyckel (en lista över alla regionsslutpunkter finns i [dokument om Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)).

### <a name="url-query-string"></a>URL-frågesträng

Frågesträngen för ansiktsslutpunktens webbadress anger vilka ansiktsattribut som ska hämtas. Du kanske vill ändra den här strängen beroende på den avsedda användningen.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>URL för bildkälla
URL:en för källa anger bilden som ska användas som indata. Du kan ändra det så att det pekar på en bild du vill analysera.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Köra kommandot

När du har gjort dina ändringar öppnar du en kommandotolk och anger det nya kommandot. Du bör se ansiktsinformationen som visas som JSON-data i konsolfönstret. Exempel:

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

I den här snabbstarten har du skrivit ett cURL-kommando som anropar Azure ansikts-API för att identifiera ansikten på en bild och returnera deras attribut. Läs ansikts-API-referensdokumentationen om du vill veta mer.

> [!div class="nextstepaction"]
> [Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)