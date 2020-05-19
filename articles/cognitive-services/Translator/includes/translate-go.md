---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 4b1ba4baa424a39a7f3433c2f2045f607712aa25
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586678"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Go-projekt med valfri IDE eller valfritt redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `translate-text.go`.

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

Det här exemplet försöker läsa prenumerations nyckeln och slut punkten för din översättare från dessa miljövariabler: `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` och `TRANSLATOR_TEXT_ENDPOINT` . Om du inte är bekant med miljövariabler kan du ange `subscriptionKey` och `endpoint` som strängar och kommentera ut villkors satserna.

Kopiera den här koden till projektet:

```go
func main() {
    /*
    * Read your subscription key from an env variable.
    * Please note: You can replace this code block with
    * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
    * want to use env variables. If so, be sure to delete the "os" import.
    */
    if "" == os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_SUBSCRIPTION_KEY.")
    }
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY")
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/translate?api-version=3.0"
    /*
     * This calls our breakSentence function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    translate(subscriptionKey, uri)
}
```

## <a name="create-a-function-to-translate-text"></a>Skapa en funktion för att översätta text

Nu ska vi skapa en funktion för att översätta text. Den här funktionen tar ett enskilt argument, prenumerations nyckeln för Translator.

```go
func translate(subscriptionKey string, uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Därefter ska vi skapa URL:en. URL:en skapas med metoderna `Parse()` och `Query()`. Lägg märke till att parametrar läggs till med `Add()`-metoden. I det här exemplet översätter du från engelska till tyska och italienska: `de` och `it`.

Kopiera den här koden till funktionen `translate`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
q.Add("to", "de")
q.Add("to", "it")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Mer information om slut punkter, vägar och parametrar för begäran finns i [Translator 3,0: Översätt](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

## <a name="create-a-struct-for-your-request-body"></a>Skapa en struktur för begärandetexten

Skapa sedan en anonym struktur för begärandetexten och koda den som JSON med `json.Marshal()`. Lägg till den här koden i funktionen `translate`.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Hello, world!"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>Skapa begäran

Nu när du har kodat begär ande texten som JSON kan du bygga din POST-begäran och anropa Translator.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Om du använder en Cognitive Services-prenumeration med flera tjänster måste du också ta med `Ocp-Apim-Subscription-Region` i parametrarna för begäran. [Lär dig mer om att autentisera med multi-service-prenumerationen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>Hantera och skriva ut svaret

Lägg till den här koden i funktionen `translate` för att avkoda JSON-svaret. Formatera och skriv sedan ut resultatet.

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

Det innebär att du har skapat ett enkelt program som anropar översättaren och returnerar ett JSON-svar. Nu är det dags att köra programmet:

```console
go run translate-text.go
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Exempelsvar

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

Ta en titt på API-referensen för att förstå allt du kan göra med Translator.

> [!div class="nextstepaction"]
> [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
