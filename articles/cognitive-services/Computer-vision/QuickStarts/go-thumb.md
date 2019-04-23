---
title: 'Snabbstart: Skapa en miniatyrbild – REST, Go'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av API för visuellt innehåll och Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4f13a384cb32596d191625c8f13bc914686c6297
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000189"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-go-in-computer-vision"></a>Snabbstart: Generera en miniatyrbild med hjälp av REST-API:et och Go i Visuellt innehåll

I den här snabbstarten har generera du en miniatyr från en avbildning med hjälp av REST-API för visuellt innehåll. Du anger höjden och bredden som kan skilja sig i proportionerna från inmatad bild. Visuellt innehåll använder smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha [Go](https://golang.org/dl/) installerat.
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnadsfri utvärderingsversion nyckel från [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) att prenumerera på visuellt innehåll och få din nyckel.

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Så här skapar du och kör exemplet:

1. Kopiera följande kod till en textredigerare.
1. Gör nedanstående ändringar i koden där det behövs:
    1. Ersätt värdet för `subscriptionKey` med din prenumerationsnyckel.
    1. Ersätt värdet för `uriBase` med slutpunktsadressen för metoden [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) från Azure-regionen där du fått dina prenumerationsnycklar om det behövs.
    1. Du kan också ersätta värdet för `imageUrl` med webbadressen till en annan bild från vilken du vill generera en miniatyrbild.
1. Spara koden som en fil med tillägget `.go`. Till exempel `get-thumbnail.go`.
1. Öppna ett kommandotolksfönster.
1. Kompilera paketet från filen genom att köra kommandot `go build` i kommandotolken. Till exempel `go build get-thumbnail.go`.
1. Kör det kompilerade paketet i kommandotolken. Till exempel `get-thumbnail`.

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
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
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

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the JSON data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the JSON result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>Granska svaret

Om åtgärden lyckas innehåller svaret binärdata för miniatyrbilden. Om begäran misslyckas innehåller svaret en felkod och ett meddelande som beskriver vad som gick fel.

## <a name="next-steps"></a>Nästa steg

Utforska den API för visuellt innehåll för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera utskrivna och handskriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
