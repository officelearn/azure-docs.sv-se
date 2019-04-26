---
title: 'Snabbstart: Analysera en lokal bild – REST, cURL'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten analyserar du en lokal bild med hjälp av API:et för visuellt innehåll med cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f76b61d3cc39f8ae35e1bf03316e7618b7136380
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409047"
---
# <a name="quickstart-analyze-a-local-image-using-the-rest-api-and-curl-in-computer-vision"></a>Snabbstart: Analysera en lokal bild med hjälp av REST API:et och cURL i Visuellt innehåll

I den här snabbstarten analyserar du en lokalt lagrad bild för att extrahera visuella funktioner med hjälp av REST API:et för visuellt innehåll. Med metoden [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (Analysera bild) kan du extrahera visuella funktioner baserat på bildinnehåll.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha [cURL](https://curl.haxx.se/windows).
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnadsfri utvärderingsversion nyckel från [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) att prenumerera på visuellt innehåll och få din nyckel.

## <a name="create-and-run-the-sample-command"></a>Skapa och köra exempelkommandot

Så här skapar du och kör exemplet:

1. Kopiera följande kommando till en textredigerare.
1. Gör följande ändringar i kommandot där det behövs:
    1. Ersätt värdet för `<subscriptionKey>` med din prenumerationsnyckel.
    1. Ersätt webbadressen för begäran (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze`) med slutpunktsadressen för metoden [Analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) från den Azure-region där du fått dina prenumerationsnycklar om det behövs.
    1. Ersätt värdet för `<localImage>` med den fullständiga sökvägen och filnamnet för den bild som du vill analysera. Till exempel `@C:/Pictures/ImageToAnalyze.jpg`.
    1. Du kan också ändra språkparametern för webbadressen (`language=en`) att använda ett annat språk som stöds.
1. Öppna ett kommandotolksfönster.
1. Klistra in kommandot från textredigeraren i kommandotolkens fönster och kör sedan kommandot.

```console
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary "<localImage>"
```

## <a name="examine-the-response"></a>Granska svaret

Ett svar som anger att åtgärden lyckades returneras i JSON. Exempelprogrammet parsar och visar ett lyckat svar i kommandotolkens fönster enligt följande exempel:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Stäng kommandotolkens fönster och textredigeraren när de inte längre behövs.

## <a name="next-steps"></a>Nästa steg

Utforska API:et för visuellt innehåll, som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
