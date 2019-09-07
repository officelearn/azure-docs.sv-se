---
title: 'Snabbstart: Anropa API:et för textanalys med hjälp av Go'
titleSuffix: Azure Cognitive Services
description: Få information och kod exempel som hjälper dig att snabbt komma igång med API för textanalys i Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 5c97648bd11a506d3c818584ed7d82d0a12d2e2c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387493"
---
# <a name="quickstart-using-go-to-call-the-text-analytics-cognitive-service"></a>Snabbstart: Anropa den kognitiva tjänsten för textanalys med hjälp av Go 
<a name="HOLTop"></a>

Den här artikeln visar hur du [identifierar språk](#Detect), [analyserar attityder](#SentimentAnalysis), [extraherar diskussionsämnen](#KeyPhraseExtraction) och [identifierar länkade entiteter](#Entities) Med  [API:er för textanalys](//go.microsoft.com/fwlink/?LinkID=759711)  med Go.

Se [API-definitionerna](//go.microsoft.com/fwlink/?LinkID=759346) för teknisk dokumentation för API:erna.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]


<a name="Detect"></a>

## <a name="detect-language"></a>Identifiera språk

API:et för språkidentifiering identifierar språket i ett textdokument, med metoden [Detect Language](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) (Identifiera språk).

1. Skapa miljövariabler `TEXT_ANALYTICS_SUBSCRIPTION_KEY` och `TEXT_ANALYTICS_ENDPOINT` för resursens Azure-slutpunkt och prenumerations nyckel. Om du har skapat de här miljövariablerna när du började redigera programmet måste du stänga och öppna den redigerare, IDE eller Shell som du använder för att få åtkomst till miljövariablerna.
1. Skapa ett nytt Go-projekt i valfri kodredigerare.
1. Lägg till koden nedan.
1. Spara filen med tillägget .go.
1. Öppna en kommando tolk på en dator med go installerat från rotmappen.
1. Skapa filen, till exempel: `go build detect.go`.
1. Kör filen, till exempel: `go run detect.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
    if "" == os.Getenv(subscriptionKeyVar) {
        log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
    }
    var subscriptionKey string = os.Getenv(subscriptionKeyVar)
    var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
    if "" == os.Getenv(endpointVar) {
        log.Fatal("Please set/export the environment variable " + endpointVar + ".")
    }
    var endpoint string = os.Getenv(endpointVar)

    const uriPath = "/text/analytics/v2.1/languages"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "text": "This is a document written in English."},
        {"id": "2", "text": "Este es un document escrito en Español."},
        {"id": "3", "text": "这是一个用中文写的文件"},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="detect-language-response"></a>Svar från språkidentifiering

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analysera sentiment

API:et för attitydanalys identifierar attityden i en uppsättning textposter, med metoden [Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) (Attityd). I följande exempel poängsätts två dokument, ett på engelska och ett annat på spanska.

1. Skapa miljövariabler `TEXT_ANALYTICS_SUBSCRIPTION_KEY` och `TEXT_ANALYTICS_ENDPOINT` för resursens Azure-slutpunkt och prenumerations nyckel. Om du har skapat de här miljövariablerna när du började redigera programmet måste du stänga och öppna den redigerare, IDE eller Shell som du använder för att få åtkomst till miljövariablerna.
1. Skapa ett nytt Go-projekt i valfri kodredigerare.
1. Lägg till koden nedan.
1. Spara filen med tillägget .go.
1. Öppna en kommando tolk på en dator med go installerat från rotmappen.
1. Skapa filen, till exempel: `go build sentiment.go`.
1. Kör filen, till exempel: `go run sentiment.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
    if "" == os.Getenv(subscriptionKeyVar) {
        log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
    }
    var subscriptionKey string = os.Getenv(subscriptionKeyVar)
    var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
    if "" == os.Getenv(endpointVar) {
        log.Fatal("Please set/export the environment variable " + endpointVar + ".")
    }
    var endpoint string = os.Getenv(endpointVar)

    const uriPath = "/text/analytics/v2.1/sentiment"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="analyze-sentiment-response"></a>Svar från attitydanalys

Resultatet mäts som positivt om det beräknas närmare 1,0 och negativt om det beräknas närmare 0,0.
Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Extrahera nyckelfraser

API:et för extrahering av diskussionsämnen extraherar diskussionsämnen från ett textdokument, med metoden [Key Phrases](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) (Diskussionsämnen). I följande exempel extraheras diskussionsämnen för både engelska och spanska dokument.

1. Skapa miljövariabler `TEXT_ANALYTICS_SUBSCRIPTION_KEY` och `TEXT_ANALYTICS_ENDPOINT` för resursens Azure-slutpunkt och prenumerations nyckel. Om du har skapat de här miljövariablerna när du började redigera programmet måste du stänga och öppna den redigerare, IDE eller Shell som du använder för att få åtkomst till miljövariablerna.
1. Skapa ett nytt Go-projekt i valfri kodredigerare.
1. Lägg till koden nedan.
1. Spara filen med tillägget .go.
1. Öppna en kommandotolk på en dator där Go är installerat.
1. Skapa filen, till exempel: `go build key-phrases.go`.
1. Kör filen, till exempel: `go run key-phrases.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
    if "" == os.Getenv(subscriptionKeyVar) {
        log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
    }
    var subscriptionKey string = os.Getenv(subscriptionKeyVar)
    var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
    if "" == os.Getenv(endpointVar) {
        log.Fatal("Please set/export the environment variable " + endpointVar + ".")
    }
    var endpoint string = os.Getenv(endpointVar)
    
    const uriPath = "/text/analytics/v2.1/keyPhrases"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
        {"id": "3", "language": "en", "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="extract-key-phrases-response"></a>Svar från extrahering av diskussionsämnen

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identifiera entiteter

API:et för entiteter identifierar välkända entiteter i ett textdokument med hjälp av [metoden Entiteter](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634). [Entiteter](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) extraherar ord från text, t. ex. "USA", och ger dig sedan typen och/eller Wikipedia-länken för dessa ord. Typen för "USA" är `location`, medan länken till Wikipedia är. `https://en.wikipedia.org/wiki/United_States`  I följande exempel identifieras entiteter för engelska dokument.

1. Skapa miljövariabler `TEXT_ANALYTICS_SUBSCRIPTION_KEY` och `TEXT_ANALYTICS_ENDPOINT` för resursens Azure-slutpunkt och prenumerations nyckel. Om du har skapat de här miljövariablerna när du började redigera programmet måste du stänga och öppna den redigerare, IDE eller Shell som du använder för att få åtkomst till miljövariablerna.
1. Skapa ett nytt Go-projekt i valfri kodredigerare.
1. Lägg till koden nedan.
1. Spara filen med tillägget .go.
1. Öppna en kommandotolk på en dator där Go är installerat.
1. Skapa filen, till exempel: `go build entities.go`.
1. Kör filen, till exempel: `go run entities.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
    if "" == os.Getenv(subscriptionKeyVar) {
        log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
    }
    var subscriptionKey string = os.Getenv(subscriptionKeyVar)
    var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
    if "" == os.Getenv(endpointVar) {
        log.Fatal("Please set/export the environment variable " + endpointVar + ".")
    }
    var endpoint string = os.Getenv(endpointVar)
    
    const uriPath = "/text/analytics/v2.1/entities"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "Microsoft is an It company."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="entity-extraction-response"></a>Svar vid entitetsextrahering

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel:

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Se också

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)
