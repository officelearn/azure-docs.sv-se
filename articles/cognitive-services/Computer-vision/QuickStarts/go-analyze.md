---
title: 'Snabbstart: Analysera en fjärrbild – REST, Go – Visuellt innehåll'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten analyserar du en bild med hjälp av API:et för visuellt innehåll med Go.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: 57c8309af47e71226f41df8cce255e73f33b27c5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341046"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-go-in-computer-vision"></a>Snabbstart: Analysera en fjärrbild med hjälp av REST API:et och Go i Visuellt innehåll

I den här snabbstarten analyserar du en fjärrlagrad bild för att extrahera visuella funktioner med hjälp av REST API:et för visuellt innehåll. Med metoden [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (Analysera bild) kan du extrahera visuella funktioner baserat på bildinnehåll.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha [Go](https://golang.org/dl/) installerat.
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan skaffa en prenumerationsnyckel genom att följa anvisningarna i [Skaffa prenumerationsnycklar](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Så här skapar du och kör exemplet:

1. Kopiera följande kod till en textredigerare.
1. Gör nedanstående ändringar i koden där det behövs:
    1. Ersätt värdet för `subscriptionKey` med din prenumerationsnyckel.
    1. Ersätt värdet för `uriBase` med slutpunktsadressen för metoden [Analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) från den Azure-region där du fått dina prenumerationsnycklar om det behövs.
    1. Du kan också ersätta värdet för `imageUrl` med webbadressen till en annan bild som du vill analysera.
1. Spara koden som en fil med tillägget `.go`. Till exempel `analyze-image.go`.
1. Öppna ett kommandotolksfönster.
1. Kompilera paketet från filen genom att köra kommandot `go build` i kommandotolken. Till exempel `go build analyze-image.go`.
1. Kör det kompilerade paketet i kommandotolken. Till exempel `analyze-image`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace <Subscription Key> with your valid subscription key.
    const subscriptionKey = "<Subscription Key>"

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the West Central US region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze"
    const imageUrl =
        "http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg"

    const params = "?visualFeatures=Description&details=Landmarks&language=en"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add request headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the JSON data from the byte array
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the JSON result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>Granska svaret

Ett svar som anger att åtgärden lyckades returneras i JSON. Exempelprogrammet parsar och visar ett lyckat svar i kommandotolkens fönster enligt följande exempel:

```json
{
  "categories": [
    {
      "detail": {
        "landmarks": []
      },
      "name": "outdoor_water",
      "score": 0.9921875
    }
  ],
  "description": {
    "captions": [
      {
        "confidence": 0.916458423253597,
        "text": "a large waterfall over a rocky cliff"
      }
    ],
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
    ]
  },
  "metadata": {
    "format": "Jpeg",
    "height": 959,
    "width": 1280
  },
  "requestId": "a92f89ab-51f8-4735-a58d-507da2213fc2"
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort det kompilerade paketet och filen som paketet kompilerades från när du inte längre behöver dem. Stäng sedan kommandotolkens fönster och textredigeraren.

## <a name="next-steps"></a>Nästa steg

Utforska API:et för visuellt innehåll, som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
