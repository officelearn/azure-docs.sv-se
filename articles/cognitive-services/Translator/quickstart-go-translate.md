---
title: 'Snabbstart: Översätta text – Translator Text, Go'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du översätta text från ett språk till ett annat med Translator Text-API:et med Go.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: df8b938c9541a4f57a337065af0ab02fe6815c6d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126708"
---
# <a name="quickstart-translate-text-with-go"></a>Snabbstart: Översätta text med Go

I den här snabbstarten ska du översätta text från ett språk till ett annat med Translator Text-API:et.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste installera [Go-distributionen](https://golang.org/doc/install) för att köra koden. Exempelkoden använder endast **core**-bibliotek, så det finns inga externa beroenden.

För att använda Translator Text-API:et behöver du också en prenumerationsnyckel. Mer information finns i [How to sign up for the Translator Text API](translator-text-how-to-signup.md) (Så här registrerar du dig för Translator Text-API:et).

## <a name="translate-request"></a>Översättningsbegäran

Följande kod översätter källtext från ett språk till ett annat med hjälp av metoden [Translate](./reference/v3-0-translate.md) (Översätt).

1. Skapa ett nytt Go-projekt i valfri kodredigerare.
2. Lägg till koden nedan.
3. Ersätt värdet `subscriptionKey` med en giltig åtkomstnyckel för din prenumeration.
4. Spara filen med tillägget .go.
5. Öppna en kommandotolk på en dator där Go är installerat.
6. Skapa filen, till exempel ”go build quickstart-translate.go”.
7. Kör filen, till exempel: ”quickstart-translate”.

```golang
package translate

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
    const uriPath = "/translate?api-version=3.0"

    // Translate to German and Italian
    const params = "&to=de&to=it"

    const uri = uriBase + uriPath + params

    const text = "Hello, world!"

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

## <a name="translate-response"></a>Översättningssvar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg

Utforska Go-paket för API:er för Cognitive Services via [Azure SDK för Go](https://github.com/Azure/azure-sdk-for-go) på GitHub.

> [!div class="nextstepaction"]
> [Utforska Go-paket på GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
