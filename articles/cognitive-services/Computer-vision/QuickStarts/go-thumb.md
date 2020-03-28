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
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb7ef0d087bc33c9e59ecd95973bc55a5ab6521
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74961530"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-with-go"></a>Snabbstart: Generera en miniatyrbild med REST-API:et för visuellt innehåll med Start

I den här snabbstarten skapar du en miniatyrbild från en bild med REST-APIN för visuellt innehåll. Du anger höjd och bredd, som kan skilja sig i proportioner från indatabilden. Visuellt innehåll använder smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) konto innan du börjar.

## <a name="prerequisites"></a>Krav

- Du måste ha [Go](https://golang.org/dl/) installerat.
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnadsfri testversionsnyckel från [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Du kan också följa instruktionerna i [Skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Datorseende och få din nyckel. Skapa sedan [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och `COMPUTER_VISION_SUBSCRIPTION_KEY` tjänstslutpunktssträngen, med namnet respektive `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Så här skapar du och kör exemplet:

1. Kopiera följande kod till en textredigerare.
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
    // Add your Computer Vision subscription key and endpoint to your environment variables.
    subscriptionKey := os.Getenv("COMPUTER_VISION_SUBSCRIPTION_KEY")
    if (subscriptionKey == "") {
        log.Fatal("\n\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n" +
            "**Restart your shell or IDE for changes to take effect.**\n")

    endpoint := os.Getenv("COMPUTER_VISION_ENDPOINT")
    if ("" == endpoint) {
        log.Fatal("\n\nSet the COMPUTER_VISION_ENDPOINT environment variable.\n" +
            "**Restart your shell or IDE for changes to take effect.**")
    }
    const uriBase = endpoint + "vision/v2.1/generateThumbnail"
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

Utforska API:et för visuellt innehåll för att analysera en bild, upptäcka kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
