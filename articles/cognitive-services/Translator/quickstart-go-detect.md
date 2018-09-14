---
title: Identifiera språk från text med Translator Text och Go | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: I den här snabbstarten ska du identifiera språket i källtexten med hjälp av Translator Text-API:et med Go i Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: 29fac1a079455a65cc3d430c3030fed99f5cfce1
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "43771289"
---
# <a name="quickstart-identify-language-from-text-with-go"></a>Snabbstart: Identifiera språk i text med Go

I den här snabbstarten ska du identifiera språket i källtexten med hjälp av Translator Text-API:et.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste installera [Go-distributionen](https://golang.org/doc/install) för att köra koden. Exempelkoden använder endast **core**-bibliotek, så det finns inga externa beroenden.

För att använda Translator Text-API:et behöver du också en prenumerationsnyckel. Mer information finns i [How to sign up for the Translator Text API](translator-text-how-to-signup.md) (Så här registrerar du dig för Translator Text-API:et).

## <a name="detect-request"></a>Identifieringsbegäran

Följande kod identifierar språket i källtexten med hjälp av metoden [Detect](./reference/v3-0-detect.md) (Identifiera).

1. Skapa ett nytt Go-projekt i valfri kodredigerare.
2. Lägg till koden nedan.
3. Ersätt värdet `subscriptionKey` med en giltig åtkomstnyckel för din prenumeration.
4. Spara filen med tillägget .go.
5. Öppna en kommandotolk på en dator där Go är installerat.
6. Skapa filen, till exempel ”go build quickstart-detect.go”.
7. Kör filen, till exempel: ”quickstart-detect”.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/detect?api-version=3.0"

    const uri = uriBase + uriPath

    const text = "Salve, mondo!"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="detect-response"></a>Svar från identifieringen

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
[
  {
    "alternatives": [
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "pt",
        "score": 1
      },
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "en",
        "score": 1
      }
    ],
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "language": "it",
    "score": 1
  }
]
```

## <a name="next-steps"></a>Nästa steg

Utforska Go-paket för API:er för Cognitive Services via [Azure SDK för Go](https://github.com/Azure/azure-sdk-for-go) på GitHub.

> [!div class="nextstepaction"]
> [Utforska Go-paket på GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
