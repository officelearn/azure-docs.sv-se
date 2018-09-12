---
title: 'Snabbstart: Skapa en miniatyrbild med API:et för visuellt innehåll och Go | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: I den här snabbstarten ska du skapa en miniatyrbild från en bild med hjälp av Visuellt innehåll med Go i Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 02ac30d295b0637b97187dbb7e1fbb1b10a2972b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772252"
---
# <a name="quickstart-generate-a-thumbnail---rest-go"></a>Snabbstart: Skapa en miniatyrbild – REST, Go

I den här snabbstarten ska du generera en miniatyrbild från en bild med hjälp av Visuellt innehåll.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att använda Visuellt innehåll behöver du en prenumerationsnyckel. Mer information finns i avsnittet [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Hämta prenumerationsnycklar).

## <a name="get-thumbnail-request"></a>Begäran om att hämta miniatyrbild

Med metoden [Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Hämta miniatyrbild) kan du skapa en miniatyrbild av en bild. Du anger höjden och bredden, som kan skilja sig från den ursprungliga bildens proportioner. Visuellt innehåll använder smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

För att köra exemplet följer du dessa steg:

1. Kopiera följande kod till ett redigeringsprogram.
1. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
1. Ändra värdet `uriBase` till den plats där du hämtade dina prenumerationsnycklar om det behövs.
1. Du kan också ändra värdet `imageUrl` till den bild som du vill analysera.
1. Spara filen med tillägget `.go`.
1. Öppna en kommandotolk på en dator där Go är installerat.
1. Skapa filen, till exempel: `go build get-thumbnail.go`.
1. Kör filen, till exempel: `get-thumbnail`.

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
    // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
    const subscriptionKey = "<Subscription Key>"

    // You must use the same location in your REST call as you used to get your
    // subscription keys. For example, if you got your subscription keys from
    // westus, replace "westcentralus" in the URL below with "westus".
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the Http client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the Post request, passing the image URL in the request body
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

    // Parse the Json data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the Json result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="get-thumbnail-response"></a>Svar från miniatyrhämtning

Om åtgärden lyckas innehåller svaret binärfilen för miniatyrbilden. Om begäran misslyckas innehåller svaret en felkod och ett meddelande som beskriver vad som gick fel.

## <a name="next-steps"></a>Nästa steg

Utforska API:erna för visuellt innehåll som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text. Du kan snabbt experimentera med API:erna för visuellt innehåll genom att prova [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API:er för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
