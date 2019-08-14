---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 39f78e78ade206b73e64796e8d25243e20bb146d
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968603"
---
## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* [Go](https://golang.org/doc/install)
* En Azure-prenumerationsnyckel för Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt go-projekt med hjälp av din favorit-IDE/-redigerare eller ny mapp på Skriv bordet. Kopiera sedan kodfragmentet till projektet/mappen i en fil med namnet `alt-translations.go`.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>Skapa Main-funktionen

Det här exemplet kommer att försöka läsa din Translator Text-prenumerationsnyckel från miljövariabeln `TRANSLATOR_TEXT_KEY`. Om du inte känner till miljövariabler kan du ange `subscriptionKey` som en sträng och kommentera bort den villkorliga instruktionen.

Kopiera den här koden till projektet:

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables. Then, be sure to delete the "os" import.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our altTranslations function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    altTranslations(subscriptionKey)
}
```

## <a name="create-a-function-to-get-alternate-translations"></a>Skapa en funktion för att få alternativa översättningar

Låt oss skapa en funktion för att få alternativa översättningar. Den här funktionen använder ett enda argument, din Translator Text-prenumerationsnyckel.

```go
func altTranslations(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Därefter ska vi skapa URL:en. URL:en skapas med metoderna `Parse()` och `Query()`. Lägg märke till att parametrar läggs till med `Add()`-metoden. I det här exemplet översätter vi från engelska till spanska.

Kopiera den här koden till funktionen `altTranslations`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0")
q := u.Query()
q.Add("from", "en")
q.Add("to", "es")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Mer information om slutpunkter, vägar och att begära parametrar finns i [Translator Text API 3.0: Slå upp i ordlista](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

## <a name="create-a-struct-for-your-request-body"></a>Skapa en struktur för begärandetexten

Skapa sedan en anonym struktur för begärandetexten och koda den som JSON med `json.Marshal()`. Lägg till den här koden i funktionen `altTranslations`.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Pineapples"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>Skapa begäran

Nu när du har kodat begärandetexten som JSON, kan du skapa din POST-begäran och anropa Translator Text API.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Om du använder en Cognitive Services-prenumeration med flera tjänster måste du också ta `Ocp-Apim-Subscription-Region` med i parametrarna för begäran. [Lär dig mer om att autentisera med multi-service](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)-prenumerationen.

## <a name="handle-and-print-the-response"></a>Hantera och skriva ut svaret

Lägg till den här koden i funktionen `altTranslations` för att avkoda JSON-svaret. Formatera och skriv sedan ut resultatet.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det var allt – du har skapat ett enkelt program som anropar Translator Text API och returnerar en JSON-svar. Nu är det dags att köra programmet:

```console
go run alt-translations.go
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Exempelsvar

```json
[
  {
    "displaySource": "pineapples",
    "normalizedSource": "pineapples",
    "translations": [
      {
        "backTranslations": [
          {
            "displayText": "pineapples",
            "frequencyCount": 158,
            "normalizedText": "pineapples",
            "numExamples": 5
          },
          {
            "displayText": "cones",
            "frequencyCount": 13,
            "normalizedText": "cones",
            "numExamples": 5
          },
          {
            "displayText": "piña",
            "frequencyCount": 5,
            "normalizedText": "piña",
            "numExamples": 3
          },
          {
            "displayText": "ganks",
            "frequencyCount": 3,
            "normalizedText": "ganks",
            "numExamples": 2
          }
        ],
        "confidence": 0.7016,
        "displayTarget": "piñas",
        "normalizedTarget": "piñas",
        "posTag": "NOUN",
        "prefixWord": ""
      },
      {
        "backTranslations": [
          {
            "displayText": "pineapples",
            "frequencyCount": 16,
            "normalizedText": "pineapples",
            "numExamples": 2
          }
        ],
        "confidence": 0.2984,
        "displayTarget": "ananás",
        "normalizedTarget": "ananás",
        "posTag": "NOUN",
        "prefixWord": ""
      }
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg

Ta en titt på API-referensen för att förstå allt du kan göra med Translator Text API.

> [!div class="nextstepaction"]
> [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
